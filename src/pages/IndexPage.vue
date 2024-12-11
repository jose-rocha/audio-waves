<script setup lang="ts">
import { ref } from 'vue';
import PlayerWaveSurfer from 'components/PlayerWaveSurfer.vue';
// import AudioPlayerVueAudioVisual from 'components/PlayerVueAudioVisual.vue';

const player = ref<HTMLAudioElement>();
const playbackRate = ref(1.0);
// const waveform = ref<HTMLDivElement>();

const setPlaybackRate = () => {
  // playbackRate mostra a velocidade atual do áudio
  if (player.value && player.value.playbackRate < 2.0) {
    player.value.playbackRate += 0.5;
    playbackRate.value += 0.5;

    return;
  }

  if (player.value) player.value.playbackRate = 1;
  playbackRate.value = 1;
};

if (player.value) player.value.addEventListener('ratechange', setPlaybackRate);
</script>

<template>
  <q-page class="row items-center justify-evenly bg-grey">
    <div class="full-width">
      <h4 class="text-center">Player WaveSurfer.JS</h4>
      <div class="flex flex-center">
        <audio v-show="false" ref="player" src="/audio-example.mp3" controls preload="metadata" />
      </div>

      <player-wave-surfer v-if="player" :player="player" />
    </div>
  </q-page>
</template>
