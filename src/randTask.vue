<script setup>
import { ref, onMounted } from 'vue'

const API = import.meta.env.VITE_API_URL || 'http://localhost:3001'

const tasks = ref([])
const loading = ref(true)
const submitting = ref(false)
const deleting = ref(null)
const randomTask = ref(null)
const isPicking = ref(false)

const form = ref({
  title: '',
  description: ''
})

async function fetchTasks() {
  try {
    const res = await fetch(`${API}/api/tasks`)
    tasks.value = await res.json()
  } catch (e) {
    console.error('Failed to fetch tasks', e)
  } finally {
    loading.value = false
  }
}

async function addTask() {
  if (!form.value.title.trim()) return
  submitting.value = true
  try {
    const res = await fetch(`${API}/api/tasks`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        title: form.value.title.trim(),
        description: form.value.description.trim()
      })
    })
    const newTask = await res.json()
    tasks.value.unshift(newTask)
    form.value = { title: '', description: '' }
  } catch (e) {
    console.error('Failed to add task', e)
  } finally {
    submitting.value = false
  }
}

async function deleteTask(id) {
  deleting.value = id
  try {
    await fetch(`${API}/api/tasks/${id}`, { method: 'DELETE' })
    tasks.value = tasks.value.filter(t => t.id !== id)
    if (randomTask.value?.id === id) randomTask.value = null
  } catch (e) {
    console.error('Failed to delete task', e)
  } finally {
    deleting.value = null
  }
}

function pickRandom() {
  if (tasks.value.length === 0) return
  isPicking.value = true
  randomTask.value = null

  let flashes = 0
  const max = 10 + Math.floor(Math.random() * 8)
  const interval = setInterval(() => {
    const idx = Math.floor(Math.random() * tasks.value.length)
    randomTask.value = tasks.value[idx]
    flashes++
    if (flashes >= max) {
      clearInterval(interval)
      isPicking.value = false
    }
  }, 100)
}

onMounted(fetchTasks)
</script>

<template>
  <v-container class="mt-6">
    <h2 class="text-h5 mb-4 text-white">Random Task Picker</h2>

    <!-- Add task form -->
    <v-card border="sm" class="text-white mb-6" color="grey-darken-4">
      <v-card-title class="text-white">Add a Task</v-card-title>
      <v-card-text>
        <v-row dense>
          <v-col cols="12">
            <v-text-field
                v-model="form.title"
                label="Task title"
                placeholder="e.g. Clean the desk"
                variant="outlined"
                density="comfortable"
                hide-details="auto"
                @keyup.enter="addTask"
            />
          </v-col>
          <v-col cols="12">
            <v-textarea
                v-model="form.description"
                label="Description (optional)"
                placeholder="Any extra details..."
                variant="outlined"
                density="comfortable"
                rows="2"
                auto-grow
                hide-details
            />
          </v-col>
          <v-col cols="12" class="d-flex justify-end">
            <v-btn
                color="green"
                :loading="submitting"
                :disabled="!form.title.trim()"
                @click="addTask"
            >
              Add Task
            </v-btn>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>

    <!-- Random picker -->
    <v-card border="sm" class="text-white mb-6 text-center" color="grey-darken-4">
      <v-card-text class="py-6">
        <div v-if="randomTask" class="mb-4">
          <p class="text-overline mb-1" style="opacity: 0.6">You should do...</p>
          <p class="text-h5 font-weight-medium text-white mb-1">{{ randomTask.title }}</p>
          <p v-if="randomTask.description" class="text-body-2" style="opacity: 0.6">
            {{ randomTask.description }}
          </p>
        </div>
        <div v-else class="mb-4">
          <p class="text-body-1" style="opacity: 0.6">
            {{ tasks.length === 0 ? 'Add some tasks first!' : 'Hit the button to pick a random task.' }}
          </p>
        </div>
        <v-btn
            size="large"
            color="green"
            :disabled="tasks.length === 0"
            :loading="isPicking"
            variant="elevated"
            @click="pickRandom"
        >
          Pick for me
        </v-btn>
      </v-card-text>
    </v-card>

    <!-- Task list -->
    <div v-if="loading" class="d-flex justify-center py-8">
      <v-progress-circular indeterminate color="green" />
    </div>

    <template v-else>
      <p class="text-overline mb-2" style="opacity: 0.6">
        {{ tasks.length }} task{{ tasks.length !== 1 ? 's' : '' }}
      </p>

      <v-row v-if="tasks.length">
        <v-col
            v-for="task in tasks"
            :key="task.id"
            cols="12" sm="6" md="4"
        >
          <v-card
              border="sm"
              class="text-white"
              color="grey-darken-4"
              :style="randomTask?.id === task.id ? 'border-green' : ''"
          >
            <v-card-title class="text-white d-flex align-center justify-space-between">
              <span>{{ task.title }}</span>
              <v-chip v-if="randomTask?.id === task.id" color="green" size="x-small" class="ml-2">Picked</v-chip>
            </v-card-title>
            <v-card-text style="opacity: 0.7">
              {{ task.description || 'No description.' }}
            </v-card-text>
            <v-card-actions>
              <v-spacer />
              <v-btn
                  color="red-lighten-1"
                  variant="text"
                  size="small"
                  :loading="deleting === task.id"
                  @click="deleteTask(task.id)"
              >
                <v-icon>mdi-delete-outline</v-icon>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>

      <v-card v-else border="sm" class="text-white" color="grey-darken-4">
        <v-card-text class="text-center" style="opacity: 0.6">
          <v-icon size="48" class="mb-2">mdi-clipboard-list-outline</v-icon>
          <p>No tasks yet. Add your first one above!</p>
        </v-card-text>
      </v-card>
    </template>

  </v-container>
</template>

<style scoped>
</style>
