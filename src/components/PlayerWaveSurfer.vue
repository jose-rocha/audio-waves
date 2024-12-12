<script setup lang="ts">
import { onMounted, ref } from 'vue';
import WaveSurfer from 'wavesurfer.js';
// Usei esse de exemplo https://wavesurfer.xyz/examples/?zoom.js
// e esse https://wavesurfer.xyz/examples/?all-options.js
import { getCssVar } from 'quasar';

const props = defineProps<{ player: HTMLAudioElement }>();
const isPlaying = ref(false);
const audioDuration = ref(0);
const audioInProgress = ref();
const wavesurfer = ref();
const audioSpeed = ref([1, 1.5, 2]);
const audioSpeedValue = ref(0);

const playAudio = () => {
  isPlaying.value = true;
  wavesurfer.value.play();
};

const pauseAudio = () => {
  wavesurfer.value.pause();
  isPlaying.value = false;
};

const calculateTime = (secs: number) => {
  const minutes = Math.floor(secs / 60);
  const seconds = Math.floor(secs % 60);
  const returnedSeconds = seconds < 10 ? `0${seconds}` : `${seconds}`;
  return `${minutes}:${returnedSeconds}`;
};

const setPlaybackRate = () => {
  if (audioSpeedValue.value < 2) {
    audioSpeedValue.value += 1;
    wavesurfer.value?.setPlaybackRate(audioSpeed.value[audioSpeedValue.value], true);
  } else {
    audioSpeedValue.value = 0;
    wavesurfer.value?.setPlaybackRate(audioSpeed.value[audioSpeedValue.value], true);
  }
};

onMounted(() => {
  wavesurfer.value = WaveSurfer.create({
    container: '#waveform',
    waveColor: '#BDBDBD',
    progressColor: `${getCssVar('primary')}`,
    url: props.player.src,
    barWidth: 5, // largura da barra
    barHeight: 1, // altura da barra
    height: 40, // altura do waveform
    cursorWidth: 2, // largura do cursor
    barGap: 1,
    barRadius: 2,
    audioRate: 1,
  });

  wavesurfer.value.on('interaction', () => {
    console.log('clicou nas ondas');
    wavesurfer.value.play();
    isPlaying.value = true;
  });

  // https://wavesurfer.xyz/examples/?soundcloud.js
  wavesurfer.value.on('decode', (duration: number) => {
    // console.log('decode', duration);
    audioDuration.value = duration;
  });
  wavesurfer.value?.on('timeupdate', (currentTime: number) => {
    audioInProgress.value = currentTime;
  });

  wavesurfer.value?.on('finish', () => {
    isPlaying.value = false;
  });
});
</script>

<template>
  <div class="full-width flex flex-center" style="justify-content: center">
    <div
      ref="audio-player-container"
      class="row"
      style="background-color: #2a3942; width: 70%; height: 50px; border-radius: 5px"
    >
      <div class="col full-height flex flex-center">
        <q-icon
          v-if="!isPlaying"
          name="mdi-play"
          color="primary"
          size="40px"
          class="cursor-pointer"
          @click="playAudio"
        />

        <q-icon
          v-else
          name="mdi-pause"
          color="primary"
          size="40px"
          class="cursor-pointer"
          @click="pauseAudio"
        />
      </div>

      <div class="col-8 full-height flex justify-center items-center">
        <div
          class="col-2 full-height flex flex-center"
          style="color: #bdbdbd; transform: translateY(2px)"
        >
          {{ !audioInProgress ? calculateTime(audioDuration) : calculateTime(audioInProgress) }}
        </div>
        <!--  Waves -->
        <div id="waveform" class="col q-px-sm cursor-pointer" />
      </div>

      <div class="col full-height flex flex-center">
        <q-avatar v-if="isPlaying" @click="setPlaybackRate">
          <!-- <slot name="icon-user" />-->
          <q-chip color="primary" text-color="white" class="q-pa-sm col cursor-pointer" dense>
            <q-tooltip anchor="top middle" self="bottom middle" :offset="[0, 10]">
              Altere velocidade do áudio
            </q-tooltip>
            <span>
              {{
                audioSpeed[audioSpeedValue] !== 1.5
                  ? `${audioSpeed[audioSpeedValue]}.0`
                  : audioSpeed[audioSpeedValue]
              }}x
            </span>
          </q-chip>
        </q-avatar>
      </div>
    </div>
  </div>
</template>
