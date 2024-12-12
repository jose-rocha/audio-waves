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
      <h4 class="text-center">
        <b>Player WaveSurfer.JS</b>
      </h4>

      <div class="text-center">
        <span>Site com o tutorial que eu usei para manipular o componente áudio</span> <br />
        <a href="https://css-tricks.com/lets-create-a-custom-audio-player/" target="_blank">
          https://css-tricks.com/lets-create-a-custom-audio-player/
        </a>
        <br />

        <span class="text-bold">Lib que usei para minipular as ondas do áudio</span> <br />
        <a href="https://wavesurfer.xyz/" target="_blank"> wavesurfer.js </a>
        <br />

        <q-btn round color="white" href=" https://wavesurfer.xyz/" target="_blank">
          <q-avatar hidden>
            <img src="logo-wavesurfer.png" />
          </q-avatar>
        </q-btn>
      </div>
      <br />
      <div class="flex flex-center">
        <audio v-show="false" ref="player" src="/audio-example.mp3" controls preload="metadata" />
      </div>

      <player-wave-surfer v-if="player" :player="player" />
    </div>
  </q-page>
</template>
