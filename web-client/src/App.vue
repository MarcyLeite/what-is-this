<template>
  <v-app>
    <v-app-bar>
      <template #prepend>
        <v-app-bar-nav-icon @click="drawer = !drawer" />
      </template>
      <v-app-bar-title> Prova </v-app-bar-title>
    </v-app-bar>
    <v-navigation-drawer v-model:model-value="drawer" temporary>
      <v-list-item link title="Home" to="/" />
      <v-list-item link title="Revise um Trabalho" to="/review/" />
    </v-navigation-drawer>
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script lang="ts" setup>
  import { ref } from 'vue'

  const drawer = ref(false)
</script>
