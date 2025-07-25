<template>
  <main class="container">
    <h1>Lista de tarefas</h1>
    <form @submit.prevent="addTask">
      <input v-model="newTask" placeholder="Nova tarefa" />
      <button type="submit">Adicionar</button>
    </form>

    <div class="filters">
      <button @click="filter = 'all'">Todas</button>
      <button @click="filter = 'pending'">Pendentes</button>
      <button @click="filter = 'done'">Concluídas</button>
    </div>

    <ul>
      <TodoItem
        v-for="task in filteredTasks"
        :key="task.id"
        :task="task"
        @toggle="toggleTask"
        @remove="removeTask"
      />
    </ul>
  </main>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'
import TodoItem from '../components/TodoItem.vue'
import type { Task } from '../types/Task'

const newTask = ref('')
const tasks = ref<Task[]>([])
const filter = ref<'all' | 'pending' | 'done'>('all')

onMounted(() => {
  const saved = localStorage.getItem('tasks')
  if (saved) tasks.value = JSON.parse(saved)
})

watch(tasks, () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}, { deep: true })

const filteredTasks = computed(() => {
  if (filter.value === 'all') return tasks.value
  if (filter.value === 'done') return tasks.value.filter(t => t.done)
  return tasks.value.filter(t => !t.done)
})

function addTask() {
  if (!newTask.value.trim()) return
  tasks.value.push({
    id: Date.now(),
    title: newTask.value.trim(),
    done: false
  })
  newTask.value = ''
}

function toggleTask(id: number) {
  const task = tasks.value.find(t => t.id === id)
  if (task) task.done = !task.done
}

function removeTask(id: number) {
  tasks.value = tasks.value.filter(t => t.id !== id)
}
</script>

<style scoped>
.container {
  max-width: 500px;
  margin: auto;
  padding: 2rem;
  font-family: Arial, sans-serif;
}

form input {
  padding: 0.5rem;
  margin-right: 0.5rem;
  width: 70%;
}

form button {
  padding: 0.5rem 1rem;
}

.filters button {
  margin: 0.5rem 0.2rem;
}

ul {
  list-style: none;
  padding: 0;
}
</style>