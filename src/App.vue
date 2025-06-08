<template>
  <div>
    <header>
      
    </header>

    <!-- Tady už zavoláme naši komponentu -->
    <TodoApp />
  </div>
</template>

<script setup>
// Importujeme komponentu z cesty, kde máš soubor TodoApp.vue
import TodoApp from './components/ToDoApp.vue';
</script>

<style scoped>

</style>