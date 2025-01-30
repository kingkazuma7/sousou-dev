<script setup>
import { ref } from  "vue";

const emit = defineEmits(['sendMessage']); // emit可能なイベント名sendMessageを定義
const sendMessageToParent = () => {
  // console.log(newMessage);
  emit('sendMessage', '子で書き換えた!'); // イベント発火させる
}
</script>

<template>
  <div style="border: 1px solid blue;"> <!-- 子要素に青色ボーダーを追加 -->
    <button @click="sendMessageToParent">親へ</button>
  </div>
</template>