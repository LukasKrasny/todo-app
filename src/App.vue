<template>
  <div class="min-h-screen bg-white dark:bg-gray-900 text-black dark:text-white px-2 sm:px-4">
    <div class="max-w-4xl mx-auto py-8">
      <header class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mb-6">
        <h1 class="text-2xl sm:text-3xl font-bold">ToDo Aplikace</h1>
        <button @click="toggleDark"
          class="px-4 py-2 border rounded hover:bg-gray-200 dark:hover:bg-gray-700 transition">
          Přepnout tmavý režim 🌙
        </button>
      </header>

      <TodoApp />
    </div>
  </div>
</template>

<script setup>
import TodoApp from './components/ToDoApp.vue'
import { onMounted } from 'vue'

const toggleDark = () => {
  const html = document.documentElement
  html.classList.toggle('dark')
  localStorage.theme = html.classList.contains('dark') ? 'dark' : 'light'
}

onMounted(() => {
  if (localStorage.theme === 'dark') {
    document.documentElement.classList.add('dark')
  }
})
</script>