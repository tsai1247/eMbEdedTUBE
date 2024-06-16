<template>
  <div class="ma-2 mt-4">
    <v-row>
      <v-col
        cols="12"
        md="6"
      >
        <videoView></videoView>
      </v-col>
      <v-col
        cols="12"
        md="6"
      >
        <lyrics></lyrics>
      </v-col>
    </v-row>
  </div>

</template>

<script setup>
  import lyrics from './Lyrics.vue'
  import videoView from './VideoView.vue'
</script>

<style scoped>
</style>
