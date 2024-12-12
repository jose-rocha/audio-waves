<script setup lang="ts">
import { ref } from 'vue';
import { useAVBars } from 'vue-audio-visual';

const props = defineProps<{ player: HTMLAudioElement }>();
const isPlaying = ref(false);
const showWavesFake = ref(true);
const audioDuration = ref(0);
const audioInProgress = ref();
const canvas = ref<HTMLCanvasElement>();

const playAudio = () => {
  props.player.play();
  isPlaying.value = true;

  setTimeout(() => {
    showWavesFake.value = false;
  }, 1000);
};

const pauseAudio = () => {
  props.player.pause();
  isPlaying.value = false;
};

const calculateTime = (secs: number) => {
  const minutes = Math.floor(secs / 60);
  const seconds = Math.floor(secs % 60);
  const returnedSeconds = seconds < 10 ? `0${seconds}` : `${seconds}`;
  return `${minutes}:${returnedSeconds}`;
};

props.player.addEventListener('ended', () => {
  isPlaying.value = false;
});

props.player.addEventListener('loadedmetadata', () => {
  // indica o estado de prontidão da mídia
  audioDuration.value = props.player.duration;
});

props.player.addEventListener('timeupdate', () => {
  // seekSlider.value = Math.floor(audio.currentTime);
  // console.log('timeupdate', Math.floor(props.player.currentTime));
  audioInProgress.value = Math.floor(props.player.currentTime);
  // audioInProgress.value = Math.floor(props.player.currentTime);
});

useAVBars(props.player, canvas, {
  src: props.player.src,
  canvHeight: 40,
  canvWidth: 200,
  barColor: '#64B5F6',
});

// player.value?.addEventListener('play', () => {
//   console.log('play');
//   wavesurfer.value.play();
// });
</script>

<template>
  <div class="full-width flex flex-center" style="justify-content: center">
    <div class="full-width">
      <div id="waveform">
        <!-- the waveform will be rendered here -->
      </div>
    </div>
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
          style="color: #bdbdbd; transform: translateY(7px)"
        >
          {{
            !isPlaying
              ? calculateTime(audioDuration)
              : audioInProgress !== undefined
                ? calculateTime(audioInProgress)
                : '0:00'
          }}
        </div>

        <div v-if="showWavesFake" class="full-height flex flex-center col">
          <img
            src="/waves.png"
            alt="Waves"
            style="width: 90%; height: 30px; object-fit: fill; transform: translateY(-2px)"
          />
        </div>

        <canvas
          v-show="!showWavesFake"
          ref="canvas"
          class="col q-px-sm cursor-pointer"
          style="height: 32px; transform: translateY(-5px)"
        />
      </div>

      <slot name="velocidade-audio" :isplaying="isPlaying" />
    </div>
  </div>
</template>
