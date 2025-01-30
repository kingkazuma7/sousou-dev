<script setup>
import { ref } from 'vue';
const message = ref('');

const updateMessage = (newMessage) => {
  message.value = newMessage; // リアクティブ変数
};
</script>

<template>
  <div>
    <div class="todo-container">
      <Todo
        @sendMessage="updateMessage"
        />
        {{ message }}
    </div>
  </div>
</template>