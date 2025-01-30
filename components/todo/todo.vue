<script setup>
import { ref, onMounted } from 'vue'

const newTodo = ref('')
const todos = ref([])

const todoStorage = {
  STORAGE_KEY: 'todos-vuejs-demo',
  fetch: () => { // データを取り出す関数
    if (process.client) {
      const todos = JSON.parse(
        localStorage.getItem(todoStorage.STORAGE_KEY) || '[]'
      )
      todos.forEach((todo, index) => {
        todo.id = index
      })
      return todos
    }
    return []
  },
  save: (todos) => { // データを保存する関数
    if (process.client) {
      localStorage.setItem(todoStorage.STORAGE_KEY, JSON.stringify(todos))
    }
  }
}

// クライアントサイドでのみ初期データを読み込む
onMounted(() => {
  todos.value = todoStorage.fetch()
})

// TODOを追加する関数
const addTodo = () => {
  if (newTodo.value.trim()) {
    const todo = {
      id: todos.value.length, // TODOのID
      text: newTodo.value, // TODOの内容
      completed: false // 完了フラグ
    }
    todos.value.push(todo)
    todoStorage.save(todos.value)
    newTodo.value = '' // 入力フィールドをクリア
  }
}
</script>

<template>
  <div class="todo-wrapper">
    <h2>TODO個別の内容が入る</h2>
    
    <!-- テスト用の入力フォーム -->
    <div class="todo-input">
      <input 
        v-model="newTodo"
        type="text"
        placeholder="新しいTODOを入力"
        @keyup.enter="addTodo"
      >
      <button @click="addTodo">追加</button>
    </div>

    <!-- 保存されているTODOの表示 -->
    <div class="todo-list">
      <ul>
        <li v-for="todo in todos" :key="todo.id">
          {{ todo.text }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
</style>
