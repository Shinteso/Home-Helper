<script setup>
import { ref, computed } from 'vue'
import home from './home.vue'
import MacroHub from './macroHub.vue'
import randTask from "@/randTask.vue";

const routes = {
  '/': home,
  '/macroHub': macroHub,
  '/randTask' : randTask,
}

const currentPath = ref(window.location.hash)
const drawer = ref(false)

window.addEventListener('hashchange', ()=> {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>
  <v-app>
    <v-navigation-drawer class="text-white"
                         color="black"
                         v-model="drawer">
      <v-list-item
          prepend-icon="mdi-home"
          href="#/"
          title="Home"
          @click="drawer = !drawer"
      ></v-list-item>
      <v-list-item
          prepend-icon="mdi-information-variant-circle-outline"
          href="#/randTask"
          title="Random Task Selector"
          @click="drawer = !drawer"
      ></v-list-item>
      <v-list-item
         prepend-icon="mdi-information-variant-circle-outline"
         href="#/macroHub"
         title="Macro-Hub"
         @click="drawer = !drawer"
      ></v-list-item>
    </v-navigation-drawer>
    <v-app-bar color="black"
               class="text-white"
               height="130">
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-app-bar-title class="text-h1 mainHeader">Home-Helper!</v-app-bar-title>
    </v-app-bar>
    <v-main style="background-color: black; color: white;">
      <component :is="currentView"></component>/
    </v-main>
    <v-footer app="false" style="background-color: black; color: white;" >Copyright 2026</v-footer>
  </v-app>
</template>

<style scoped>
.mainHeader {
  font-family: "Mystery Quest", system-ui;
  font-weight: 400;
  font-style: normal;
  line-height: 1;
}

</style>


