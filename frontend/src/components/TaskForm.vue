<template>
  <form @submit.prevent="addTask" class="mb-4">
    <input v-model="title" type="text" placeholder="Tytuł zadania" class="border p-2 mr-2 rounded" required>
    <input v-model="description" type="text" placeholder="Opis zadania" class="border p-2 mr-2 rounded">
    <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded">Dodaj</button>
  </form>
</template>

<script setup>
import { ref } from 'vue'
import api from '../api'

const emit = defineEmits(['taskAdded'])

const title = ref('')
const description = ref('')

const addTask = async () => {
  await api.post('/tasks', { title: title.value, description: description.value })
  title.value = ''
  description.value = ''
  emit('taskAdded')
}
</script>
