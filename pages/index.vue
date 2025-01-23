<template>
  <div class="container">
    <AVWaveform
      :src="Song"
      noplayed-line-color="#929292"
      played-line-color="#FFFFFF"
      canv-height="40"
      canv-width="500"
    />
    <div class="audio-controls">
      <div class="time-display">
        <span>{{ currentTime }}</span>
        <span>/</span>
        <span>{{ duration }}</span>
      </div>
      <button @click="togglePlay">
        {{ isPlaying ? "Pause" : "Play" }}
      </button>
    </div>
    <audio
      ref="audioRef"
      :src="Song"
      @play="isPlaying = true"
      @pause="isPlaying = false"
      @timeupdate="updateTime"
      @loadedmetadata="setDuration"
    ></audio>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import { AVWaveform } from "vue-audio-visual";
import Song from "~/assets/audio/song.mp3";

const audioRef = ref<HTMLAudioElement | null>(null);
const isPlaying = ref(false);
const currentTime = ref("0:00");
const duration = ref("0:00");

const formatTime = (seconds: number) => {
  const minutes = Math.floor(seconds / 60);
  const remainingSeconds = Math.floor(seconds % 60);
  return `${minutes}:${remainingSeconds.toString().padStart(2, "0")}`;
};

const togglePlay = () => {
  if (audioRef.value) {
    if (isPlaying.value) {
      audioRef.value.pause();
    } else {
      audioRef.value.play();
    }
  }
};

const updateTime = () => {
  if (audioRef.value) {
    currentTime.value = formatTime(audioRef.value.currentTime);
  }
};

const setDuration = () => {
  if (audioRef.value) {
    duration.value = formatTime(audioRef.value.duration);
  }
};
</script>

<style scoped>
.container {
  background-color: black;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  color: white;
}

.audio-controls {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-top: 10px;
}

.time-display {
  display: flex;
  gap: 5px;
}

button {
  background-color: white;
  color: black;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
}
</style>
