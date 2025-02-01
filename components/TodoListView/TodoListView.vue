<script setup>
import { ref } from 'vue';
import { statuses } from "../../src/const/status";
const props = defineProps({
  items: {
    type: Array,
    required: true
  }
});

/**
 * localStorageがない時のケア
 * items反映されない..refがないから更新されない
 */
const isLocalStorageAvailable = typeof localStorage !== 'undefined';
let items = ref(JSON.parse((isLocalStorageAvailable && localStorage.getItem("items"))) || []);
let editingId = ref(null); // 編集中idを取得
let isErrMsg = ref(false);
let isShowmodal = ref(false);
let deleteItemId = null; // 削除対象のインデックス

// ---------------- 編集
const onEdit = (id) => {
  const item = items.value[id];
  editingId.value = id;
  items.value[id] = {
    ...item,
    onEdit: true
  };
}

// ---------------- 完了
const onUpdate = (id) => {
  if (!editingId.value) return;

  const newItem = {
    ...items.value[id],
    content: items.value[id].content,
    limit: items.value[id].limit,
    state: items.value[id].state,
    onEdit: false,
  }

  // 更新したnewItemリストが空だったら
  if (newItem.content == "" || newItem.limit == "") {
    isErrMsg.value = true;
    return;
  }

  items.value.splice(id, 1, newItem); // 入れ替えたいid番目、1つのアイテムを、newItemに。
  localStorage.setItem("items", JSON.stringify(items.value)) // 更新されたリストで再保存
  editingId.value = null; // 編集中の状態を解除
  isErrMsg.value = false; // 更新完了したらfalseに戻す
}

// ---------------- 削除
const showDeleteModal = (index) => {
  isShowmodal.value = true;
  deleteItemId = index; // インデックスを保存
}
const onDeleteItem = () => {
  isShowmodal.value = false;
  items.value.splice(deleteItemId, 1);
  localStorage.setItem("items", JSON.stringify(items.value)); // localStorageを更新
}
const onHideModal = () => {
  isShowmodal.value = false;
}
</script>

<template>
  <div>
    <!-- エラー -->
    <p class="error-message" v-if="isErrMsg">タスク・期限を両方入力してください</p>
    <!-- モーダル -->
    <div class="modal" v-if="isShowmodal">
      <div class="modal-content">
        <p>削除しても良いですか</p>
        <button @click="onDeleteItem()">はい</button>
        <button @click="onHideModal()">いいえ</button>
      </div>
    </div>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>内容</th>
          <th>期限</th>
          <th>状態</th>
          <th>編集</th>
          <th>削除</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>{{ item.id }}</td>
          <td>
            <!-- 初期false, 反転で内容表示、trueの時だけinput表示 -->
            <span v-if="!item.onEdit">{{ item.content }}</span> 
            <input v-else type="text" v-model="item.content">
          </td>
          <td>
            <span v-if="!item.onEdit">{{ item.limit }}</span>
            <input v-else type="date" v-model="item.limit">
          </td>
          <td>
            <span v-if="!item.onEdit">{{ item.state.value }}</span>
            <select v-else v-model="item.state">
              <option
                v-for="state in statuses"
                :key="state.id"
                :value="state"
              >
                {{ state.value }}
              </option>
            </select>
          </td>
          <td>
            <button v-if="!item.onEdit" @click="onEdit(index)">編集</button>
            <button v-else @click="onUpdate(index)">完了</button>
          </td>
          <td><button @click="showDeleteModal(index)">削除</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
/* エラーメッセージ用のスタイル */
.error-message {
  color: red;
  font-weight: bold;
  margin: 0.5rem 0;
}
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  max-width: 400px;
  width: 100%;
  text-align: center;
}
</style>