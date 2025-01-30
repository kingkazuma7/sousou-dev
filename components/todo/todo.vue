<script setup>
import { ref } from  "vue";
const name = ref('');
</script>

<template>
  <div>
    <label for="name">名前:</label>
    <input type="text" name="name" id="" v-model="name">
    {{ name }}
  </div>
</template>