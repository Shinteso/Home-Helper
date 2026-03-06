<script setup>
const Macros = [
  {
    title: "Revolution Macro",
    img: "./public/Revolution_Macro.png",
    description:"Macro for Bee Swarm",
  },
  {
    title: "UTD Macro",
    img: "./public/UTD.png",
    description:"Macro for Universal Tower Defense",
  },
]
</script>

<template>
<<v-container class="mt-6">
  <h2 class="text-h5 mb-4">Different Macros (No Live Updates Yet waiting for Webhook Integration)</h2>
  <v-row>
    <v-col
        v-for="item in Macros"
        :key="item.title"
        cols="12" sm="6" md="4">
      <v-card border="sm" class="text-white" color="grey-darken-4">
        <v-img :src="item.image" height="180" cover/>
        <v-card-title>{{ item.title }}</v-card-title>
        <v-card-text>{{ item.description }}</v-card-text>
      </v-card>
    </v-col>
  </v-row>
</v-container>
</template>
