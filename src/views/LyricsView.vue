<template>
  <div>
    <div
      v-for="(val, key) in hiragana"
      :key="key"
    >
      {{ val }}
    </div>
    <p />
    {{ lyrics }}
    <!-- <span
      class="playing-lyrics"
      v-for="(item, index) in displayingLyricList"
      :key="index"
    >
      <span v-if="item.hiragana">
        <ruby>
          {{ item.kanji }}<rt>{{ item.hiragana }}</rt>
          <rp>( 瀏覽器不支援 )</rp>
        </ruby>
      </span>
      <span v-else>
        {{ item.kanji }}
      </span>
    </span> -->
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue';
import * as wanakana from 'wanakana';

const props = defineProps({
  lyrics: String,
  hiragana: Array,
});

const displayingLyricList = ref([]);

watch(() => props.lyrics, () => {
  if(props.lyrics && props.hiragana) {
    let tokenizedText = [];
    const hiragana = props.hiragana;
    tokenizedText = wanakana.tokenize(props.lyrics).reduce(
      (acc, curr) => {
        if (curr === '\n') {
          acc.push([]);
        } else {
          if (acc.length === 0) {
            acc.push([]);
          }
          acc[acc.length - 1].push(curr);
        }
        return acc;
      }, []
    );
    console.log(hiragana);
    console.log(tokenizedText);

    // displayingLyricList.value = tokenizedText.map( (line, i) => {
    //   let hiraganaStartIndex = 0;
    //   return line.map((item, j) => {
    //     if(wanakana.isKanji(item)) {
    //       if (j + 1 < line.length) {
    //         const end = hiragana[i].indexOf(line[j+1], hiraganaStartIndex);
    //         const result = {
    //           kanji: item,
    //           hirigana: hiragana[i].substring(hiraganaStartIndex, end),
    //         }
    //         i = end;
    //         return result;
    //       }
    //       else {
    //         return {
    //           kanji: item,
    //           hiragana: hiragana[i].substring(hiraganaStartIndex),
    //         }
    //       }
    //     }
    //     else {
    //       if (item !== ' ') {
    //         hiraganaStartIndex += item.length;
    //       }
    //       return {
    //         kanji: item,
    //         hiragana: null,
    //       }
    //     }
    //   });
    // });

    console.log(displayingLyricList.value);
  }

})
/*
愛はどこからやってくるのでしょう 自分の胸に問いかけた
ニセモノなんか興味はないの ホントだけを見つめたい
[

]
 */

</script>

<style scoped>
  .playing-lyrics {
    font-size: 24px;
    line-height: 60px;
    margin-left: 15px;
    white-space: pre-line;
  }
</style>
