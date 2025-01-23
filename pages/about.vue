<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { useAVWaveform } from "vue-audio-visual";
import Song from "~/assets/audio/song.mp3";

const player = ref<HTMLAudioElement | null>(null);
const canvas = ref<HTMLCanvasElement | null>(null);
const isPlaying = ref(false);
const currentTime = ref(0);
const duration = ref(0);

// Format time to MM:SS
const formatTime = (timeInSeconds: number) => {
  const minutes = Math.floor(timeInSeconds / 60);
  const seconds = Math.floor(timeInSeconds % 60);
  return `${minutes.toString().padStart(2, "0")}:${seconds
    .toString()
    .padStart(2, "0")}`;
};

// Computed properties for time display
const currentTimeFormatted = computed(() => formatTime(currentTime.value));
const remainingTimeFormatted = computed(() =>
  formatTime(duration.value - currentTime.value)
);

// Toggle play/pause
const togglePlay = () => {
  if (!player.value) return;

  if (isPlaying.value) {
    player.value.pause();
  } else {
    player.value.play();
  }
  isPlaying.value = !isPlaying.value;
};

// Update time tracking
const updateTime = () => {
  if (player.value) {
    currentTime.value = player.value.currentTime;
  }
};

onMounted(() => {
  if (player.value) {
    player.value.addEventListener("timeupdate", updateTime);
    player.value.addEventListener("loadedmetadata", () => {
      duration.value = player.value?.duration || 0;
    });
  }

  useAVWaveform(player, canvas, {
    src: Song,
    canvHeight: 40,
    canvWidth: 200,
    barColor: "red",
    playedLineColor: "red",
    noplayedLineColor: "#929292",
  });
});
</script>

<template>
  <div class="audio-player">
    <div class="player-controls">
      <button @click="togglePlay">
        {{ isPlaying ? "Pause" : "Play" }}
      </button>
    </div>
    <audio ref="player" :src="Song" />
    <canvas ref="canvas" />
    <div class="time-display">
      <span class="playing-time">{{ currentTimeFormatted }} </span
      ><span class="remaining-time">-{{ remainingTimeFormatted }}</span>
    </div>
  </div>
</template>

<style scoped>
.audio-player {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.player-controls {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
}

button {
  padding: 5px 10px;
}

.time-display {
  display: flex;
  justify-content: space-between;
}
.playing-time {
  color: #929292;
  font-size: 8px;
  font-weight: 800;
  line-height: 13px;
}
.remaining-time {
  color: #929292;
  font-size: 8px;
  font-weight: 800;
  line-height: 13px;
}
</style>
