<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const API = import.meta.env.VITE_API_URL || 'http://localhost:3001'

const logs = ref([])
const screenshot = ref(null)
const report = ref(null)
const loading = ref(true)
let interval = null

// Fetching all the data from the Api to post onto the page
async function fetchAll() {
  try {
    const [logsRes, screenshotRes] = await Promise.all([
        fetch(`${API}/api/logs`),
        fetch(`${API}/api/screenshot`),
    ])
    logs.value = await logsRes.json()
    const screenshotData = await screenshotRes.json()
    screenshot.value = screenshotData.url ? `${API}${screenshotData.url}` :  null

    // Find latest report from logs (has fields)
    report.value = logs.value.find(log => log.embeds?.[0]?.fields?.length > 0) || null
  } catch (err) {
    console.error('Failed to fetch:', err)
  } finally {
    loading.value = false
  }
}
onMounted(() => {
  fetchAll()
  interval = setInterval(fetchAll, 5000)
})

onUnmounted(() => {
  clearInterval(interval)
})
function formatText(text) {
  if (!text) return ''
  return text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
}

function imageUrl(url) {
  if (!url) return null
  if (url.startsWith('http')) return url
  return `${API}${url}`
}
</script>

<template>
  <v-container class="mt-6">
    <!-- Header -->
    <div class="d-flex align-center mb-6">
      <v-btn variant="text" color="white" href="#/macroHub" prepend-icon="mdi-arrow-left" class="mr-4">Back</v-btn>
      <h2 class="text-h5">Revolution Macro</h2>
      <v-chip color="green" size="small" class="ml-4">Live</v-chip>
    </div>

    <v-row>
      <!-- Latest Screenshot-->
      <v-col cols="12" md="6">
        <v-card color="grey-darken-4" border="sm" class="text-white h-100">
          <v-card-title class="text-subtitle-1">Latest Screenshot</v-card-title>
          <v-card-text>
            <v-img
              v-if="screenshot"
              :src="screenshot"
              cover
              rounded="lg"
              />
            <div v-else class="text-gray text-center py-8">No screenshot yet</div>
          </v-card-text>
        </v-card>
      </v-col>

      <!-- Latest Hourly Report -->
      <v-col cols="12" md="6">
        <v-card color="grey-darken-4" border="sm" class="text-white h-100">
          <v-card-title class="text-subtitle-1">Latest Hourly Report</v-card-title>
          <v-card-text>
            <div v-if="report">
              <p
                v-if="report.embeds?.[0]?.description"
                v-html="formatText(report.embeds[0].description)"
                class="mb-3 text-body-2"
              />
              <v-divider v-if="report.embeds?.[0]?.fields?.length" class="mb-3"/>
              <div
                  v-for="field in report.embeds?.[0]?.fields"
                  :key="field.name"
                  class="mb-2">
                <div class="text-caption text-grey">{{ field.name }}</div>
                <div class="text-body-2" v-html="formatText(field.value)"/>
              </div>
              <div class="text-caption text-grey mt-4">
                {{ new Date(report.receivedAt).toLocaleString() }}
              </div>
            </div>
            <div v-else class="text-gray text-center py-8">No report yet</div>
          </v-card-text>
        </v-card>
      </v-col>

      <!-- Live Log Feed -->
      <v-col cols="12">
        <v-card color="grey-darken-4" border="sm" class="text-white">
          <v-card-title class="text-subtitle-1">
            Live Log feed
            <v-chip color="green" size="x-small" class="m1-2">Refreshing every 5s</v-chip>
          </v-card-title>
          <v-card-text>
            <div v-if="loading" class="text-center py-8">
              <v-progress-circular indeterminate color="white"/>
            </div>
            <div v-else-if="logs.length === 0" class="text-grey text-center py-8">
              No logs yet
            </div>
            <div v-else class="log-list">
              <div v-for="log in logs" :key="log.id" class="log-item">
                <div class="d-flex align-start gap-3">
                  <span class="text-caption text-grey log-tiem">
                    {{ new Date(log.receivedAt).toLocaleTimeString() }}
                  </span>
                  <span
                    class="text-body-2"
                    v-html="formatText(log.embeds?.[0]?.description || log.content || 'Empty message')"
                  />
                </div>
                <v-img
                  v-if="log.embeds?.[0]?.image"
                  :src="imageUrl(log.embeds[0].image)"
                  max-width="300"
                  class="mt-2 rounded"
                />
                <v-divider class="mt-2 mb-2"/>
              </div>
            </div>
          </v-card-text>
        </v-card>
      </v-col>

    </v-row>
  </v-container>
</template>

<style scoped>
.log-list {
  max-height: 500px;
  overflow-y: auto;
}
.log-time {
  min-width: 85px;
  padding-top: 2px;
  font-family: monospace;
}
</style>