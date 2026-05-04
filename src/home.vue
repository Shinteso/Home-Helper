<script setup>
const helperTools = [
  {
    title: "Random Task Selector",
    description: "Give you a random task then do it for a specific time!",
    href: "#/randTask",
  },
  {
    title: "Macro-Hub",
    description: "Here is where all the webhooks for all my macros will be stored",
    href: "#/macroHub",
  },
]
</script>

<template>
  <v-carousel
    crossfade="true"
    cycle="true"
    hide-delimiters="true"
    height="400"
  >
    <v-carousel-item
       src="tree.png"
       cover
    ></v-carousel-item>
    <v-carousel-item
        src="moon.png"
        cover
    ></v-carousel-item>
    <v-carousel-item
        src="mountain.png"
        cover
    ></v-carousel-item>
    <v-carousel-item
        src="starry.png"
        cover
    ></v-carousel-item>
  </v-carousel>

  <v-container class="mt-6">
    <h2 class="text-h5 mb-4">Different Pages</h2>
    <v-row>
      <v-col
          v-for="item in helperTools"
          :key="item.title"
          cols="12" sm="6" md="4">
        <v-card border="sm" class="text-white" color="grey-darken-4" :href="item.href">
          <v-img :src="item.image" height="180" cover/>
          <v-card-title>{{ item.title }}</v-card-title>
          <v-card-text>{{ item.description }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>

</style>