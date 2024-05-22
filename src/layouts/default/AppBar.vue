<template>
  <!-- <v-app-bar flat>
    <v-app-bar-title>
      <v-row class="mt-4">
        <v-col cols="2">
          <v-icon icon="mdi-youtube" />
          MeTube
        </v-col>
        <v-col cols="8">

          <v-text-field
            v-model="keyword"
            label="搜尋"
            @keyup.enter="search"
            prepend-inner-icon="mdi-magnify"
          ></v-text-field>
        </v-col>
        <v-col cols="2">
          <v-btn
            @click="search"
            rounded
          ><v-icon>mdi-magnify</v-icon></v-btn>
        </v-col>
      </v-row>
    </v-app-bar-title>

  </v-app-bar> -->
  <v-app-bar color="blue-lighten-4">
    <!-- <template v-slot:image>
      <v-img gradient="to top right, rgba(19,84,122,.8), rgba(128,208,199,.8)"></v-img>
    </template> -->

    <template v-slot:prepend>
      <v-icon icon="mdi-youtube" />
    </template>
    <!-- <v-app-bar-title>MeTube</v-app-bar-title> -->
    <div class="text-h6 mr-3">MeTube</div>
    <v-spacer></v-spacer>
    <v-text-field
      v-model="keyword"
      label="搜尋"
      variant="underlined"
      class="mt-4"
      @keyup.enter="search"
      prepend-inner-icon="mdi-magnify"
    ></v-text-field>

    <v-tooltip location="bottom">
      <template v-slot:activator="{ props }">
        <div v-bind="props">
          <v-btn
            icon
            @click="search"
          >
            <v-icon>mdi-magnify</v-icon>
          </v-btn>
        </div>
      </template>
      <span>搜尋</span>
    </v-tooltip>

    <v-tooltip location="bottom">
      <template v-slot:activator="{ props }">
        <div v-bind="props">
          <v-btn
            icon
            @click="gotoSubscription"
          >
            <v-icon>mdi-youtube-subscription</v-icon>
          </v-btn>
        </div>
      </template>
      <span>訂閱內容</span>
    </v-tooltip>

    <v-spacer></v-spacer>

    <v-tooltip location="bottom">
      <template v-slot:activator="{ props }">
        <div v-bind="props">
          <v-btn
            icon
            @click="gotoGithub"
          >
            <v-icon>mdi-github</v-icon>
          </v-btn>
        </div>
      </template>
      <span>GitHub</span>
    </v-tooltip>

  </v-app-bar>

</template>

<script setup>
  import { ref } from 'vue';
  const keyword = ref('');
  function search() {
    if(keyword.value) {
      window.location = `https://www.youtube.com/results?search_query=${keyword.value}`
    }
  }

  function gotoGithub() {
    window.location = 'https://github.com/tsai1247/embEdedTUBE';
  }

  function gotoSubscription() {
    window.location = 'https://www.youtube.com/feed/subscriptions';
  }

</script>
