<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <header>
    <nav class="flex justify-between w-full max-w-7xl mx-auto shadow-lg mb-6">
      <div class="px-5 py-2">
        <h1 class="font-bold text-xl">Todos App</h1>
      </div>
      <div class="flex justify-around px-3 py-1">
        <RouterLink to="/" class="px-5 py-2 hover:bg-gray-500 hover:text-gray-200 hover:rounded-md">Home</RouterLink>
        <RouterLink to="/about" class="px-5 py-2 hover:bg-gray-500 hover:text-gray-200 hover:rounded-md">About</RouterLink>
      </div>
    </nav>
  </header>
  
  <section class="w-full max-w-7xl mx-auto">
    <RouterView />
  </section>
</template>

<style scoped>

</style>
