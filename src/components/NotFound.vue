<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-br from-gray-800 to-black text-white p-8">
    <h1 class="text-7xl font-extrabold mb-4">404</h1>
    <p class="text-2xl mb-8">Stránka nebyla nalezena.</p>
    <router-link
      to="/"
      class="bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 px-6 rounded-lg shadow-lg transition duration-300"
    >
      Zpět na úvodní stránku
    </router-link>
  </div>
</template>

<script setup>
</script>

<style scoped>
</style>