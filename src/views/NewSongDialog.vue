<template>
  <div>
    <v-dialog
      v-model="showDialog"
      width="auto"
    >
      <v-card width="800">
        <v-card-title>
          <v-row class="ma-1">
            <v-col
              cols="*"
              align-self="center"
              class="font-weight-bold"
            >
              新增歌曲
            </v-col>
            <v-col cols="auto">
              <v-btn
                icon
                class="mx-2"
              >
                <v-icon @click="save">mdi-content-save</v-icon>
              </v-btn>

              <v-btn icon>
                <v-icon @click="close">mdi-close</v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-card-title>
        <v-card-text>
          <v-text-field
            label="歌名"
            v-model="songName"
          ></v-text-field>
          <v-text-field
            label="藝人"
            v-model="singerName"
          ></v-text-field>
          <v-textarea
            label="歌詞"
            v-model="lyrics"
          ></v-textarea>
          <v-text-field
            label="Youtube連結"
            v-model="youtubeLink"
          ></v-text-field>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { lyricsDB } from '@/common/indexedDB';
import toHiragana from '@/common/API/hiragana';

const props = defineProps({
  value: {
    type: Boolean,
    default: false,
  },
})

const emits = defineEmits(['close-dialog', 'refresh'])
const showDialog = computed({
  get() {
    return props.value;
  },
  set(val) {
    emits("close-dialog");
  }
})

const songName = ref('');
const singerName = ref('');
const lyrics = ref('');
const youtubeLink = ref('');

watch(() => showDialog.value, () => {
  if(showDialog.value) {
    songName.value = '';
    singerName.value = '';
    lyrics.value = '';
    youtubeLink.value = '';
  }
})

const save = async () => {
  const hiragana = (await toHiragana(lyrics.value))?.converted?.replaceAll('   ', '\n').replaceAll(' ', '').split('\n');
  await lyricsDB.add(songName.value, singerName.value, 'docs', lyrics.value, hiragana, youtubeLink.value);
  emits('refresh');
  emits('close-dialog');
};

const close = () => {
  emits('close-dialog');
};

</script>

<style>
</style>
