<script setup>
import { RouterLink, RouterView } from 'vue-router'

const title = "Todo App"
</script>

<template>
  <header>
    <nav class="flex justify-between w-full max-w-7xl mx-auto shadow-lg mb-6">
      <div class="px-5 py-2">
        <h1 class="font-bold text-xl">{{ title }}</h1>
      </div>
      <div class="flex justify-around px-3 py-1">
        <RouterLink to="/" class="px-5 py-2 group text-gray-800 font-semibold">
          Home
          <p class="block max-w-0 group-hover:max-w-full transition-all duration-100 h-0.5 bg-gray-800"></p>
        </RouterLink>
        <RouterLink to="/about" class="px-5 py-2 group text-gray-800 font-semibold">
          About
          <p class="block max-w-0 group-hover:max-w-full transition-all duration-100 h-0.5 bg-gray-800"></p>
        </RouterLink>
      </div>
    </nav>
  </header>
  
  <section class="w-full max-w-7xl mx-auto">
    <RouterView />
  </section>
</template>

<style scoped>

</style>
