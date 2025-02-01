<script setup>
import { ref } from  "vue";
import { statuses } from "../../src/const/status";

const emit = defineEmits(['add-todo']);
const input = ref("");
const inputDate = ref("");
const isErrMsg = ref(false); // エラーメッセージ用のフラグ

const onSubmitForm = (e) => {
  e.preventDefault();
  
  // 入力チェック
  if (input.value.trim() === "" || inputDate.value === "") {
    isErrMsg.value = true;  // エラーメッセージを表示
    return;
  }

  // エラーがなければ、エラーメッセージを非表示に
  isErrMsg.value = false;

  const items = JSON.parse(localStorage.getItem("items") || '[]');
  
  const newItem = {
    id: items.length,
    content: input.value,
    limit: inputDate.value, // 日付入力欄の値
    state: statuses.NOT_START,
    onEdit: false, // 編集フラグ
  }
  
  emit('add-todo', newItem);  // 親コンポーネントに通知
  
  items.push(newItem); // 新タスクデータ追加
  const updateItems = JSON.stringify(items);
  localStorage.setItem("items", updateItems) // 新タスクデータ追加したリストをローカルストレージに保存
}

</script>

<template>
  <div class="TodoInput">
    <!-- エラーメッセージ -->
    <p v-if="isErrMsg" class="error-message">
      タスク・期限を両方入力してください
    </p>
    
    <form @submit="onSubmitForm">
      <p><label for="">やること</label><input type="text" v-model="input"></p>
      <p><label for="">期限</label><input type="date" v-model="inputDate"></p>
      <input type="submit" value="登録!">
    </form>
  </div>
</template>

<style scoped>
.TodoInput {
  border: 1px solid blue;
  padding: 1rem;  /* 内側の余白を追加 */
}

/* エラーメッセージ用のスタイル */
.error-message {
  color: red;
  font-weight: bold;
  margin: 0.5rem 0;
}
</style>