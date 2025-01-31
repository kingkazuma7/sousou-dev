<script setup>
import { ref } from  "vue";
import { statuses } from "../../src/const/status";

const input = ref("");
const inputDate = ref("");

const onSubmitForm = (e) => {
  e.preventDefault();
  console.log(input.value);
  const items = JSON.parse(localStorage.getItem("items")) || []; // データをobjに変換して取得
  debugger
  
  const newItem = {
    id: items.length,
    content: input.value,
    limit: inputDate.value, // 日付入力欄の値
    state: statuses.NOT_START,
    onEdit: false, // 編集フラグ
  }
  items.push(newItem); // 新タスクデータ追加
  const updateItems = JSON.stringify(items);
  localStorage.setItem("items", updateItems) // 新タスクデータ追加したリストをローカルストレージに保存
}

</script>

<template>
  <div class="TodoInput">
    <form @submit="onSubmitForm">
      <p><label for="">やること</label><input type="text" v-model="input"></p>
      <p><label for="">期限</label><input type="date" v-model="inputDate"></p>
      <input type="submit" value="登録!">
    </form>
  </div>
</template>

<style>
.TodoInput {
  border: 1px solid blue;
}
</style>