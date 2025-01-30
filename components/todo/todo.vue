<script setup>
import { ref } from  "vue";
const ary = [
  { id: 1, name: "りんご" },
  { id: 2, name: "みかん" },
  { id: 3, name: "ぶどう" }
]
const num = 15;
</script>

<template>
  <div>
    <ul>
      <li :key="aryList.id" v-for="aryList in ary">{{ aryList.name }}</li>
    </ul>
    <p v-if="number < 10">数字は10より小さい</p>
    <p v-else>数字は10より大きい</p>
  </div>
</template>