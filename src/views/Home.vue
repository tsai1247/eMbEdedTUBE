<template>
  <div class="ma-2 mt-4">
    <v-row>
      <v-col cols="12">
        <search-view @update-selected-song="onUpdateSelectedSong"></search-view>
      </v-col>
    </v-row>
    <v-row>
      <v-col
        cols="12"
        md="6"
      >
        <video-view :video-id="videoId"></video-view>
      </v-col>
      <v-col
        cols="12"
        md="6"
      >
        <lyrics-view
          :lyrics="lyrics"
          :hiragana="hiragana"
        ></lyrics-view>
      </v-col>
    </v-row>
  </div>

</template>

<script setup>
  import lyricsView from './LyricsView.vue';
  import videoView from './VideoView.vue';
  import searchView from './SearchView.vue';
  import { ref, computed } from 'vue';

  const youtubeLink = ref(null);
  const lyrics = ref(null);
  const hiragana = ref(null);

  function onUpdateSelectedSong(selectedSong) {
    youtubeLink.value = selectedSong.youtube;
    lyrics.value = selectedSong.lyrics;
    hiragana.value = selectedSong.hiragana;
  }

  const videoId = computed(() => {
    return youtubeLink.value ? youtubeLink.value.split("v=")[1] : null
  });
</script>

<style scoped>
</style>
