<template>
  <li :class="{ done: task.done }">
    <input type="checkbox" :checked="task.done" @change="$emit('toggle', task.id)" />
    <span>{{ task.title }}</span>
    <button @click="$emit('remove', task.id)">🗑️</button>
  </li>
</template>

<script setup lang="ts">
import type { Task } from '../types/Task'

defineProps<{
  task: Task
}>()

defineEmits(['toggle', 'remove'])
</script>

<style scoped>
li {
  display: flex;
  align-items: center;
  margin: 0.5rem 0;
  gap: 0.5rem;
}

.done span {
  text-decoration: line-through;
  color: gray;
}
</style>