<script setup>
import { ref, onMounted } from "vue";
import TodoInput from "../TodoInput/TodoInput.vue";
import TodoListView from "../TodoListView/TodoListView.vue";

// 共有するTODOリストのデータ
const items = ref([]);

// 初期データの読み込み
onMounted(() => {
  const savedData = JSON.parse(localStorage.getItem("items")) || [];
  items.value = savedData;
});

// 新しいTODOを受け取って表示する関数
const handleAddTodo = (newItem) => {
  items.value.push(newItem);
  items.value = [...items.value]; // リアクティビティを確実
  localStorage.setItem("items", JSON.stringify(items.value));
  console.log("New item added:", newItem);
  console.log("Updated items:", items.value);
}
</script>

<template>
  <div class="todoApp">
    <TodoInput @add-todo="handleAddTodo"></TodoInput>
    <TodoListView :items="items"></TodoListView>
  </div>
</template>