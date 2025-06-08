<template>
    <div class="max-w-md mx-auto p-4">
        <!-- Nadpis aplikace -->
        <h1 class="text-2xl font-bold mb-4">ToDo App</h1>

        <!-- Formulář pro přidání nového úkolu -->
        <div class="mb-4">
            <!-- v-model propojí hodnotu inputu s proměnnou newTask -->
            <input 
                v-model="newTask" 
                    @keyup.enter="addTask"
                    placeholder="Zadej úkol" 
                    class="border rounded p-2 w-full" 
            />
            <!-- @keyup.enter="addTask" umožňuje přidat úkol i stiskem Enter -->
            <!-- Tlačítko pro přidání úkolu -->
            <button @click="addTask" class="mt-2 bg-blue-500 text-white px-4 py-2 rounded">Přidat</button>    
        </div>

        <!-- Seznam úkolů -->
        <ul>
            <!-- v-for pro iteraci přes všechny úkoly -->
            <li v-for="(task, index) in tasks" :key="index" class="flex justify-between items-center mb-2">
                
                <!-- Zobrazení úkolu pokud není v režimu editace -->
                <div v-if="!task.editing">{{ task.text }}</div>

                <!-- Formulář pro úpravu úkolu -->
                <div v-else>
                    <input v-model="task.text" class="border rounded p-1"/>
                    <button @click="task.editing = false" class="ml-2 bg-green-500 text-white px-2 py-1 rounded">Uložit</button>
                </div>

                <!-- Akční tlačítka pro editaci a smazání -->
                <div>
                    <button @click="editTask(task)" class="mr-2 text-yellow-500">✏️</button>
                    <button @click="deleteTask(index)" class="text-red-500">🗑️</button>
                </div>
            </li>   
        </ul>
    </div>
</template>

<script setup>
// Importujeme ref z Vue 3 (Composition API)
import { ref } from 'vue';

// tasks je reaktivní pole všech úkolů
const tasks = ref([])
const newTask = ref('')

// Funkce pro přidání úkolu
function addTask() {
    // Pokud je vstup prázdný, nic se nepřidá
    if (newTask.value.trim() === '') return

    // Přidáme nový úkol do pole tasks
    tasks.value.push({
    text: newTask.value,  // samotný text úkolu
    editing: false        // zda je úkol v režimu editace
    })

    // Po přidání vyprázdníme input
    newTask.value = ''
}

// Funkce pro smazání úkolu
function deleteTask (index) {
    tasks.value.splice(index, 1)
}

// Funkce pro zapnutí režimu editace u konkrétního úkolu
function editTask (task) {
    task.editing = true
}
</script>

<style scoped>

</style>