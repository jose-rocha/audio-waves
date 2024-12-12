<script setup lang="ts">
import { ref } from 'vue';
import PlayerVueAudioVisual from 'components/PlayerVueAudioVisual.vue';

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
        <b>Player Vue Audio Visual</b>
      </h4>

      <div class="text-center">
        <span>Site com o tutorial que eu usei para manipular o componente áudio</span> <br />
        <a href="https://css-tricks.com/lets-create-a-custom-audio-player/" target="_blank">
          https://css-tricks.com/lets-create-a-custom-audio-player/
        </a>

        <!-- <span>Lib que usei para miniplar as ondas do áudio</span> <br />
        <a href="https://wavesurfer.xyz/" target="_blank"> https://wavesurfer.xyz/ </a>

        logo-wavesurfer.png -->
      </div>
      <br />

      <div class="flex flex-center">
        <audio v-show="false" ref="player" src="/audio-example.mp3" controls preload="metadata" />
      </div>

      <player-vue-audio-visual v-if="player" :player="player" :playbackRate="playbackRate">
        <!-- <template #velocidade-audio="props">-->
        <template v-slot:velocidade-audio="props">
          <div class="col full-height flex flex-center">
            <q-avatar v-if="props.isplaying" @click="setPlaybackRate">
              <!-- <slot name="icon-user" />-->
              <q-chip color="primary" text-color="white" class="q-pa-sm col cursor-pointer" dense>
                <q-tooltip anchor="top middle" self="bottom middle" :offset="[0, 10]">
                  Altere velocidade do áudio
                </q-tooltip>
                <span> {{ playbackRate !== 1.5 ? `${playbackRate}.0` : playbackRate }}x </span>
              </q-chip>
            </q-avatar>
          </div>
        </template>
      </player-vue-audio-visual>
    </div>
  </q-page>
</template>
