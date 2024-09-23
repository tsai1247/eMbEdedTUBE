<template>
  <v-container>
    <div ref="playerRef"></div>
  </v-container>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';

const props = defineProps({
  videoId: {
    type: String,
    default: null
  }
})

const playerRef = ref(null);
const player = ref(null);

const loadYouTubeIframeAPI = () => {
  return new Promise((resolve) => {
    const tag = document.createElement('script');
    tag.src = 'https://www.youtube.com/iframe_api';
    const firstScriptTag = document.getElementsByTagName('script')[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
    window.onYouTubeIframeAPIReady = resolve;
  });
};

const createYouTubePlayer = () => {
  player.value = new YT.Player(playerRef.value, {
    height: '390',
    width: '640',
    videoId: props.videoId,
    playerVars: {
      showinfo: 0,  // 隱藏視頻信息
      controls: 1,  // 顯示播放器控件
    },
    events: {
      onReady: onPlayerReady,
      onStateChange: endLoop
    },
  });
};

const onPlayerReady = (event) => {
  console.log('Player is ready');
};

const endLoop = (e) => {
  if (e.data === YT.PlayerState.ENDED) {
    playVideo();
  }
}

const seekTo = (seconds) => {
  if (player.value) {
    player.value.seekTo(seconds, true);
  }
};

const formatTime = (seconds) => {
  const minutes = Math.floor(seconds / 60);
  const remainingSeconds = seconds % 60;
  return `${minutes.toString().padStart(2, '0')}:${remainingSeconds.toString().padStart(2, '0')}`;
};

const playVideo = () => {
  if (player.value) {
    player.value.playVideo();
  }
};

const pauseVideo = () => {
  if (player.value) {
    player.value.pauseVideo();
  }
};

const loadVideo = () => {
  if (player.value && props.videoId) {
    console.log(player.value);
    player.value.loadVideoById({
      videoId: props.videoId,
      playerVars: {
        showinfo: 0,  // 隱藏視頻信息
        controls: 1,  // 顯示播放器控件
        loop: 1,
        playlist: props.videoId,
      },
    });
    player.value.setLoop(true);
  }
};

watch(() => props.videoId, () => {
  loadVideo();
})

onMounted(async () => {
  await loadYouTubeIframeAPI();
  createYouTubePlayer();
});
</script>
