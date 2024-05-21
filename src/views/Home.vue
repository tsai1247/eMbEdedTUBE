<template>
  <div class="ma-2">
    <div v-if="v">

      <v-btn @click="theaterMode = !theaterMode">
        <div v-if="theaterMode">
          標準模式
        </div>
        <div v-else>
          劇院模式
        </div>
      </v-btn>
      <div class="my-1">
        <iframe
          :width="width"
          :height="height"
          :src="source"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
          referrerpolicy="strict-origin-when-cross-origin"
          allowfullscreen
        ></iframe>
      </div>
      <div
        v-for="(comment, index) in comments"
        class="ma-3"
        :key="index"
      >
        <v-chip>
          {{ comment.snippet.topLevelComment.snippet.authorDisplayName }}
        </v-chip>
        <div
          class="ml-4"
          round
        >
          <div>
            <span v-html="comment.snippet.topLevelComment.snippet.textDisplay"></span>
          </div>
        </div>
        <div
          v-if="comment.otherReplies && comment.otherReplies.items"
          class="ml-4 ma-2 mb-4"
        >
          <div
            v-for="(replies, jndex) in comment.otherReplies.items"
            :key="jndex"
            class="ma-1 mb-2"
          >
            <div>
              <v-chip>
                {{ replies.authorDisplayName }}
              </v-chip>
            </div>
            <div>
              <span>&emsp;</span>
              <span v-html="replies.textDisplay"></span>
            </div>
          </div>
        </div>
        <span
          v-if="comment.snippet.totalReplyCount && (!comment.otherReplies || comment.otherReplies.nextPageToken)"
          @click="loadReply(index)"
          class="text-blue ml-4"
          :style="{cursor: 'pointer'}"
        >
          查看{{comment.snippet.totalReplyCount}}則回覆

        </span>
      </div>
      <div v-if="key">
        <v-btn
          v-if="nextPageToken"
          @click="loadComments"
        >
          查看更多
        </v-btn>
        <v-btn
          v-else-if="comments === null"
          @click="loadComments"
        >
          讀取留言
        </v-btn>
        <div v-else>
          沒有留言
        </div>
      </div>
      <div v-else>無法讀取留言</div>
    </div>
    <div v-else>
      參數錯誤
    </div>

  </div>

</template>

<script setup>
  import { ref, watch, onMounted } from 'vue';
  import { useRoute } from 'vue-router';

  const width = ref(640);
  const height = ref(360);
  const theaterMode = ref(false);
  watch(() => theaterMode.value, () => {
    if(theaterMode.value) {
      width.value = window.outerWidth - 50;
      height.value = window.innerHeight - 150;
    }
    else {
      width.value = 640;
      height.value = 360;
    }
  })

  const route = useRoute();
  const v = ref(null);
  const t = ref(null);
  const key = ref(null);
  const source = ref('https://www.youtube.com/embed/t2cTwMe5Rbo');

  watch(() => v.value, () => {
    comments.value = null;
  })

  onMounted(() => {
    v.value = route.query.v;
    t.value = route.query.t;
    key.value = window.localStorage['youtube_data_api_key'];
    if(t.value) {
      source.value = `https://www.youtube.com/embed/${v.value}?start=${t.value}`;
    }
    else {
      source.value = `https://www.youtube.com/embed/${v.value}`;
    }
  })

  function loadReply(index) {
    if(!comments.value || index >= comments.value.length ) return;

    const APIHeader = 'https://www.googleapis.com/youtube/v3/comments';
    const parentId = comments.value[index].id;
    const part = 'snippet';
    const maxResults = 20;
    const nextPageToken = comments.value[index].otherReplies?.nextPageToken;

    let url = '';
    if(nextPageToken) {
      url = `${APIHeader}?part=${part}&parentId=${parentId}&maxResults=${maxResults}&key=${key.value}&nextPageToken=${nextPageToken}`;
    }
    else {
      url = `${APIHeader}?part=${part}&parentId=${parentId}&maxResults=${maxResults}&key=${key.value}`;
    }

    fetch(url)
      .then(response => {
        return response.json();
      }).then(result => {
        console.log(result);
        if(comments.value[index].otherReplies) {
          comments.value[index].otherReplies = {
            nextPageToken: result.nextPageToken,
            items: [...comments.value[index].otherReplies.items, ...result.items.map(comment => comment.snippet)]
          };
        }
        else {
          comments.value[index].otherReplies = {
            nextPageToken: result.nextPageToken,
            items: result.items.map(comment => comment.snippet)
          };
        }
        console.log('reply', comments.value[index].otherReplies);
      })
  }

  const nextPageToken = ref(null);
  const comments = ref(null);
  function loadComments() {
    if(!key.value) return;

    const APIHeader = 'https://www.googleapis.com/youtube/v3/commentThreads';
    const part = 'snippet,replies';
    const maxResults = 20;
    let url = '';
    if (nextPageToken.value) {
      url = `${APIHeader}?videoId=${v.value}&key=${key.value}&part=${part}&maxResults=${maxResults}&pageToken=${nextPageToken.value}`;
    }
    else {
      url = `${APIHeader}?videoId=${v.value}&key=${key.value}&part=${part}&maxResults=${maxResults}`;
    }

    fetch(url)
      .then(response => {
        return response.json();
      }).then(result => {
        nextPageToken.value = result.nextPageToken;
        if(comments.value === null) {
          comments.value = result.items;
        }
        else {
          comments.value = [...comments.value, ...result.items];
        }
        console.log(comments.value);
      })


  }

</script>

<style scoped>
</style>
