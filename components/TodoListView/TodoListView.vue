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
  let inputContent = ref(); // 内容
  let inputLimit = ref(); // 期限
  let inputState = ref(); // ステータス

const onEdit = (id) => {
  inputContent.value = items.value[id].content;
  inputLimit.value = items.value[id].limit;
  inputState.value = items.value[id].state;
  items.value[id].onEdit = true;
  // console.log(items.value[id].onEdit = true);
}

let isErrMsg = ref(false);
const onUpdate = (id) => {
  const newItem = {
    id: id,
    content: inputContent,
    limit: inputLimit,
    state: inputState,
    onEdit: false,
  }
  
  if (inputContent.value == "" || inputLimit.value == "") { // 空だったら
    isErrMsg.value = true;
    return;
  }

  items.value.splice(id, 1, newItem); // 入れ替えたいid番目、1つのアイテムを、newItemに。
  localStorage.setItem("items", JSON.stringify(items.value)) // 更新されたリストで再保存
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
        <tr v-for="item in items" :key="item.id">
          <td>{{ item.id }}</td>
          <td>
            <!-- 初期false, 反転で内容表示、trueの時だけinput表示 -->
            <span v-if="!item.onEdit">{{ item.content }}</span> 
            <input v-else type="text" v-model="inputContent">
          </td>
          <td>
            <span v-if="!item.onEdit">{{ item.limit }}</span>
            <input v-else type="date" v-model="inputLimit">
          </td>
          <td>
            <span v-if="!item.onEdit">{{ item.state.value }}</span>
            <select v-else v-model="inputState">
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
            <button v-if="!item.onEdit" @click="onEdit(item.id)">編集</button>
            <button v-else @click="onUpdate(item.id)">完了</button>
          </td>
          <td><button>削除</button></td>
          {{!item.onEdit}}
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