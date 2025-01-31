<script setup>
import { ref } from  "vue";
import TodoInput from "../TodoInput/TodoInput.vue";
import TodoListView from "../TodoListView/TodoListView.vue";

</script>

<template>
  <div class="todoApp">
    <TodoInput></TodoInput>
    <TodoListView></TodoListView>
  </div>
</template>