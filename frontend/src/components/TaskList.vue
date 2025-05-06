<template>
  <div class="p-4">
    <h2 class="text-2xl font-bold mb-4">Lista zadań</h2>
    <TaskForm @taskAdded="handleAddTask" />

    <table class="min-w-full bg-white rounded-lg shadow mt-4">
      <thead>
        <tr>
          <th class="text-left p-2">Tytuł</th>
          <th class="text-left p-2">Opis</th>
          <th class="text-left p-2">Status</th>
          <th class="text-left p-2">Akcje</th>
        </tr>
      </thead>
      <transition-group name="fade" tag="tbody">
        <tr v-for="task in tasks" :key="task.id" class="border-t">
          <td class="p-2">{{ task.title }}</td>
          <td class="p-2">{{ task.description }}</td>
          <td class="p-2">
            <span :class="task.status === 'completed' ? 'text-green-500' : 'text-yellow-500'">
              {{ task.status }}
            </span>
          </td>
          <td class="p-2 space-x-2">
            <button @click="markCompleted(task)" class="text-blue-500">Ukończ</button>
            <button @click="editTask(task)" class="text-yellow-500">Edytuj</button>
            <button @click="deleteTask(task.id)" class="text-red-500">Usuń</button>
          </td>
        </tr>
      </transition-group>
    </table>

    <div v-if="isModalOpen" class="fixed inset-0 bg-gray-500 bg-opacity-50 flex justify-center items-center z-50">
      <div class="bg-white p-6 rounded-lg w-96">
        <h3 class="text-xl font-semibold mb-4">Edytuj zadanie</h3>
        <form @submit.prevent="updateTask">
          <div class="mb-4">
            <label for="title" class="block text-sm font-medium text-gray-700">Tytuł</label>
            <input v-model="formData.title" id="title" type="text" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" required />
          </div>
          <div class="mb-4">
            <label for="description" class="block text-sm font-medium text-gray-700">Opis</label>
            <textarea v-model="formData.description" id="description" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"></textarea>
          </div>
          <div class="mb-4">
            <label for="status" class="block text-sm font-medium text-gray-700">Status</label>
            <select v-model="formData.status" id="status" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm">
              <option value="pending">Pending</option>
              <option value="completed">Completed</option>
            </select>
          </div>
          <div class="flex justify-end space-x-2">
            <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600 focus:outline-none">Zapisz</button>
            <button type="button" @click="closeModal" class="bg-gray-200 text-gray-700 px-4 py-2 rounded-md hover:bg-gray-300 focus:outline-none">Zamknij</button>
          </div>
        </form>
      </div>
    </div>

    <div v-if="toastMessage" class="fixed bottom-4 right-4 bg-gray-800 text-white px-4 py-2 rounded shadow-lg animate-fade-in-out">
      {{ toastMessage }}
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import TaskForm from './TaskForm.vue'
import api from '../api'

const tasks = ref([])
const isModalOpen = ref(false)
const formData = ref({
  title: '',
  description: '',
  status: 'pending',
})
const taskId = ref(null)

const toastMessage = ref('')
let toastTimeout = null

const showToast = (msg) => {
  toastMessage.value = msg
  clearTimeout(toastTimeout)
  toastTimeout = setTimeout(() => {
    toastMessage.value = ''
  }, 3000)
}

const fetchTasks = async () => {
  const response = await api.get('/tasks')
  tasks.value = response.data
}

const handleAddTask = () => {
  fetchTasks()
  showToast('Zadanie dodane!')
}

const deleteTask = async (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id)
  showToast('Zadanie usunięte')
  await api.delete(`/tasks/${id}`)
  fetchTasks()
}

const markCompleted = async (task) => {
  await api.put(`/tasks/${task.id}`, { ...task, status: 'completed' })
  showToast('Zadanie ukończone')
  fetchTasks()
}

const editTask = (task) => {
  formData.value = { ...task }
  taskId.value = task.id
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
}

const updateTask = async () => {
  try {
    await api.put(`/tasks/${taskId.value}`, formData.value)
    showToast('Zadanie zaktualizowane')
    fetchTasks()
    closeModal()
  } catch (error) {
    showToast('Błąd aktualizacji zadania')
    console.error('Błąd aktualizacji zadania:', error)
  }
}

onMounted(fetchTasks)
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.4s ease;
}
.fade-leave-from {
  opacity: 1;
  transform: scale(1);
}
.fade-leave-to {
  opacity: 0;
  transform: scale(0.8) translateX(-20px);
}

/* Animacja toast */
@keyframes fadeInOut {
  0% { opacity: 0; transform: translateY(20px); }
  10%, 90% { opacity: 1; transform: translateY(0); }
  100% { opacity: 0; transform: translateY(20px); }
}

.animate-fade-in-out {
  animation: fadeInOut 3s ease-in-out;
}
</style>
