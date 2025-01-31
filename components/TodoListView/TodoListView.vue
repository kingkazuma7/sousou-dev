<script setup>
import { ref, onMounted } from 'vue'

const items = ref([]);  // 最初は空の配列

// ブラウザで動いているときだけ実行する
onMounted(() => {
  // 保存されたデータを取得してitemsに保存する
  const savedData = JSON.parse(localStorage.getItem('items')) || [];
  items.value = savedData; // 取得したデータをitems.valueに保存
})

</script>

<template>
  <div>
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
          <td>{{ item.content }}</td>
          <td>{{ item.limit }}</td>
          <td>{{ item.state.value }}</td>
          <td><button>編集</button></td>
          <td><button>削除</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
</style>