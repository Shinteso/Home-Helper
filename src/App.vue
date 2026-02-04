<script setup>
import { ref, computed } from 'vue'
import home from './home.vue'
import about from './about.vue'

const routes = {
  '/': home,
  '/about': about
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', ()=> {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>
  <a href="#/">Home</a> |
  <a href="#/about">Macros</a>

  <component :is="currentView"></component>
</template>

