<script setup>
import { ref, onMounted } from 'vue'

const newTodo = ref('') // 新しいメモを書く場所
const todos = ref([]) // メモを保管する場所

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
      id: todos.value.length,
      comment: newTodo.value,
      state: '作業中'
    }
    todos.value.push(todo)
    todoStorage.save(todos.value)
    newTodo.value = ''
  }
}
</script>

<template>
  <div class="todo-wrapper">
    <h2>TODOリスト</h2>
    
    <div class="todo-input">
      <input 
        v-model="newTodo"
        type="text"
        placeholder="新しいTODOを入力"
        @keyup.enter="addTodo"
      >
      <button @click="addTodo">追加</button>
    </div>

    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>コメント</th>
          <th>状態</th>
          <th>操作</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in todos" :key="item.id">
          <th>{{ item.id }}</th>
          <td>{{ item.comment }}</td>
          <td class="state">
            <button>{{ item.state }}</button>
          </td>
          <td class="button">
            <button>削除</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1rem;
}

th, td {
  padding: 0.5rem;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.state, .button {
  text-align: center;
}

button {
  padding: 0.25rem 0.5rem;
}

.todo-input {
  margin: 1rem 0;
  display: flex;
  gap: 0.5rem;
}

input {
  padding: 0.5rem;
  flex-grow: 1;
}
</style>
