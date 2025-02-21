<template>
  <q-header class="q-py-sm">
    <q-toolbar>
      <q-btn
        unelevated
        color="white"
        text-color="secondary"
        class="text-no-wrap text-bold q-mr-sm"
        :disable="uiLocked"
        @click="playContinuously"
        >連続再生</q-btn
      >
      <q-btn
        unelevated
        color="white"
        text-color="secondary"
        class="text-no-wrap text-bold q-mr-sm"
        :disable="!nowPlayingContinuously"
        @click="stopContinuously"
        >停止</q-btn
      >

      <q-space />

      <!-- <q-btn
        unelevated
        color="white"
        text-color="secondary"
        class="text-no-wrap text-bold q-mr-sm"
        :disable="!canUndo || uiLocked"
        @click="undo"
        >元に戻す</q-btn
    >
    <q-btn
        unelevated
        color="white"
        text-color="secondary"
        class="text-no-wrap text-bold q-mr-sm"
        :disable="!canRedo || uiLocked"
        @click="redo"
        >やり直す</q-btn
    > -->
      <q-btn
        id="setting_button"
        unelevated
        round
        icon="settings"
        class="q-mr-sm"
        :disable="uiLocked"
        @click="openSettingDialog"
      />
      <q-btn
        unelevated
        color="white"
        text-color="secondary"
        class="text-no-wrap text-bold"
        :disable="uiLocked"
        @click="openHelpDialog"
        >ヘルプ</q-btn
      >
    </q-toolbar>
  </q-header>
</template>

<script lang="ts">
import { defineComponent, computed } from "vue";
import { useStore } from "@/store";
import { CAN_REDO, CAN_UNDO, REDO, UNDO } from "@/store/command";
import {
  UI_LOCKED,
  IS_HELP_DIALOG_OPEN,
  IS_SETTING_DIALOG_OPEN,
} from "@/store/ui";
import {
  PLAY_CONTINUOUSLY_AUDIO,
  STOP_CONTINUOUSLY_AUDIO,
} from "@/store/audio";
import { useQuasar } from "quasar";

export default defineComponent({
  setup() {
    const store = useStore();
    const $q = useQuasar();

    const uiLocked = computed(() => store.getters[UI_LOCKED]);
    const canUndo = computed(() => store.getters[CAN_UNDO]);
    const canRedo = computed(() => store.getters[CAN_REDO]);
    const nowPlayingContinuously = computed(
      () => store.state.nowPlayingContinuously
    );

    const undo = () => {
      store.dispatch(UNDO);
    };
    const redo = () => {
      store.dispatch(REDO);
    };
    const playContinuously = async () => {
      try {
        await store.dispatch(PLAY_CONTINUOUSLY_AUDIO, {});
      } catch {
        $q.dialog({
          title: "再生に失敗しました",
          message: "エンジンの再起動をお試しください。",
          ok: {
            label: "閉じる",
            flat: true,
            textColor: "secondary",
          },
        });
      }
    };
    const stopContinuously = () => {
      store.dispatch(STOP_CONTINUOUSLY_AUDIO, {});
    };
    const openHelpDialog = () => {
      store.dispatch(IS_HELP_DIALOG_OPEN, { isHelpDialogOpen: true });
    };
    const openSettingDialog = () => {
      store.dispatch(IS_SETTING_DIALOG_OPEN, { isSettingDialogOpen: true });
    };

    return {
      uiLocked,
      canUndo,
      canRedo,
      nowPlayingContinuously,
      undo,
      redo,
      playContinuously,
      stopContinuously,
      openHelpDialog,
      openSettingDialog,
    };
  },
});
</script>
