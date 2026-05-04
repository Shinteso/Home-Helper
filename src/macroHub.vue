<script setup>
const Macros = [
  {
    title: "Revolution Macro",
    img: "/Revolution_Macro.png",
    description:"Macro for Bee Swarm",
    href: "#/revolutionMacro",
    live: true,
  },
  {
    title: "UTD Macro (Not in Service Yet)",
    img: "/UTD.png",
    description:"Macro for Universal Tower Defense",
    href: "#/null",
    live: false,
  },
]
</script>

<template>
  <v-container class="mt-6">
    <h2 class="text-h5 mb-4">Different Macros (With Live Updates)</h2>
    <v-row>
      <v-col
          v-for="item in Macros"
          :key="item.title"
          cols="12" sm="6" md="4">
        <v-card border="sm" class="text-white" color="grey-darken-4" :href="item.href || undefined" :style="item.href ? 'cursor:pointer' : 'opacity:0.6'">
          <v-img :src="item.img" height="180" cover/>
          <v-card-title>
            {{ item.title }}
            <v-chip v-if="item.live" color="green" size="x-small" class="m1-2">Live</v-chip>
          </v-card-title>
          <v-card-text>{{ item.description }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
