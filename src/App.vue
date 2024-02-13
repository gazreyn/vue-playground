<script setup lang="ts">
import { computed, ref } from 'vue';
import AudioPlayer from './components/AudioPlayer.vue';

const audioPlayerRef = ref<InstanceType<typeof AudioPlayer> | null>(null);

function play() {
  if (!audioPlayerRef.value) return;
  audioPlayerRef.value.play();
}

function pause() {
  if (!audioPlayerRef.value) return;
  audioPlayerRef.value.pause();
}

function stop() {
  if (!audioPlayerRef.value) return;
  audioPlayerRef.value.stop();
}

function setVolume(volume: number) {
  if (!audioPlayerRef.value) return;
  audioPlayerRef.value.setVolume(volume);
}

const formattedVolume = computed(() => {
  return audioPlayerRef.value?.animatedAudioVolume.toFixed(2);
});

const targetVolume = ref(0.5);
</script>

<template>
  <div class="wrapper">
    <AudioPlayer
      class="row"
      ref="audioPlayerRef"
    />
    <div class="row">
      <button @click="play">Play</button>
      <button @click="pause">Pause</button>
      <button @click="stop">Stop</button>
    </div>
    <div class="row">
      <input
        type="range"
        min="0"
        max="1"
        step="0.1"
        v-model="targetVolume"
      />
      <button @click="setVolume(targetVolume)">
        Set Volume to {{ targetVolume }}
      </button>
    </div>

    <div class="row">Current Volume: {{ formattedVolume }}</div>
  </div>
</template>

<style>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.row {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}
</style>
