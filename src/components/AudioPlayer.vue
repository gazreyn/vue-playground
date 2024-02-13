<script setup lang="ts">
import { TransitionPresets, useTransition } from '@vueuse/core';
import { ref, watch } from 'vue';

withDefaults(
  defineProps<{
    href?: string;
    volume?: number;
  }>(),
  {
    href: 'https://s.cdpn.io/1202/Star_Wars_original_opening_crawl_1977.mp3',
    volume: 1,
  }
);

const audioVolume = ref(1);
const audioPlayer = ref<HTMLAudioElement | null>();
const currentTime = ref(0);

const animatedAudioVolume = useTransition(audioVolume, {
  duration: 1000,
  transition: TransitionPresets.linear,
});

/**
 * @param volume A number between 0-1
 */
function setVolume(volume: number) {
  if (!audioPlayer.value) return;
  if (isNaN(volume)) return;

  volume = Math.max(0, Math.min(1, volume)); // Ensures the volume is between 0 and 100
  audioVolume.value = volume;
}

function play() {
  if (!audioPlayer.value) return;
  audioPlayer.value.play();
}

function pause() {
  if (!audioPlayer.value) return;
  audioPlayer.value.pause();
}

function stop() {
  if (!audioPlayer.value) return;
  audioPlayer.value.pause();
  audioPlayer.value.currentTime = 0;
}

function onPlay() {
  emit('play');
}

function onPause() {
  emit('pause');
}

function onTimeUpdate() {
  //
  if (!audioPlayer.value) return;
  currentTime.value = audioPlayer.value.currentTime;
  emit('timeupdate', currentTime.value);
}

function onEnded() {
  emit('ended');
}

watch(animatedAudioVolume, (volume) => {
  if (!audioPlayer.value) return;
  audioPlayer.value.volume = volume;
});

const emit = defineEmits<{
  timeupdate: [time: number];
  play: [];
  pause: [];
  ended: [];
}>();

defineExpose({
  play,
  pause,
  stop,
  setVolume,
  currentTime,
  animatedAudioVolume,
});
</script>

<template>
  <div>
    <audio
      ref="audioPlayer"
      controls
      @timeupdate="onTimeUpdate"
      @play="onPlay"
      @pause="onPause"
      @ended="onEnded"
    >
      <source
        :src="href"
        type="audio/mpeg"
      />
      Your browser does not support the audio element.
    </audio>
  </div>
</template>
