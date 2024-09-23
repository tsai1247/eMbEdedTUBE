<template>
  <div class="ma-2">
    <v-row>
      <v-col cols="*">
        <v-autocomplete
          v-model="selectedSong"
          return-object
          label="search your song..."
          :items="songList"
          :item-title="item => `${item.name} - ${item.singer}`"
          item-value="id"
        >
        </v-autocomplete>
      </v-col>

      <v-col
        cols="auto"
        align="end"
      >
        <v-btn
          icon
          @click="openNewSongDialog"
        >
          <v-icon>mdi-plus-thick</v-icon>
        </v-btn>
      </v-col>
    </v-row>
    <new-song-dialog
      :value="showNewSongDialog"
      @close-dialog="showNewSongDialog = false"
      @refresh="refresh"
    ></new-song-dialog>
  </div>
</template>

<script setup>
import { lyricsDB } from '@/common/indexedDB';
import newSongDialog from './NewSongDialog.vue';
import { ref, onMounted, computed, watch } from 'vue';

const emits = defineEmits(['update-selected-song']);

const songList = ref([]);
const selectedSong = ref(null);
const youtubeLink = computed(() => {
  return selectedSong.value?.youtube;
});
const lyrics = computed(() => {
  return selectedSong.value?.lyrics;
});

watch(() => selectedSong.value, () => {
  emits('update-selected-song', selectedSong.value);
}, {deep: true});

const refresh = () => {
  return lyricsDB.getAll().then(
    (result) => {
      songList.value = result;
    }
  );
}

onMounted(() => {
  refresh();
});

const showNewSongDialog = ref(false);
const openNewSongDialog = () => {
  showNewSongDialog.value = true;
}

</script>

<style>
</style>
