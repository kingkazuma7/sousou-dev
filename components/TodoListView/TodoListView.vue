<script setup>
import { ref } from 'vue';
import { statuses } from "../../src/const/status";
const props = defineProps({
  items: {
    type: Array,
    required: true
  }
});

const localItems = ref(props.items);

watch(() => props.items, (newItem) => {
  localItems.value = newItem;
}, { deep: true })

/**
 * localStorageがない時のケア
 * items反映されない..refがないから更新されない
 */
const isLocalStorageAvailable = typeof localStorage !== 'undefined';
let items = ref(JSON.parse((isLocalStorageAvailable && localStorage.getItem("items"))) || []);
let editingId = ref(null); // 編集中idを取得
let isErrMsg = ref(false);

const onEdit = (id) => {
  const item = items.value[id];
  editingId.value = id;
  items.value[id] = {
    ...item,
    onEdit: true
  };
}

const onUpdate = (id) => {
  if (!editingId.value) return;

  const newItem = {
    ...localItems.value[id],
    // content: items.value[id].content,
    // limit: items.value[id].limit,
    // state: items.value[id].state,
    onEdit: false,
  }

  // 更新したnewItemリストが空だったら
  if (newItem.content == "" || newItem.limit == "") {
    isErrMsg.value = true;
    return;
  }

  localItems.value[id] = newItem;
  // items.value.splice(id, 1, newItem); // 入れ替えたいid番目、1つのアイテムを、newItemに。
  localStorage.setItem("items", JSON.stringify(localItems.value)) // 更新されたリストで再保存
  editingId.value = null; // 編集中の状態を解除
  isErrMsg.value = false; // 更新完了したらfalseに戻す
}
</script>

<template>
  <div>
    <p class="error-message" v-if="isErrMsg">タスク・期限を両方入力してください</p>
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
        <tr v-for="(item, index) in localItems" :key="item.id">
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
          <td><button>削除</button></td>
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
</style>