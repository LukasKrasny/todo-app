<template>
  <div class="max-w-3xl mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">ToDo App</h1>

    <!-- Samostatné úkoly -->
    <section class="mb-6 p-4 border rounded bg-white shadow">
      <h2 class="text-lg font-semibold mb-2">Jednotlivé úkoly</h2>
      <div class="flex mb-2">
        <input v-model="newGeneralTask" @keyup.enter="addGeneralTask" placeholder="Zadej úkol" class="border rounded p-2 flex-1 mr-2" />
        <button @click="addGeneralTask" class="bg-blue-600 text-white px-4 py-2 rounded">Přidat</button>
      </div>
      <ul>
        <li v-for="(task, index) in generalTasks" :key="index" class="flex justify-between items-center mb-1">
          <div v-if="!task.editing">{{ task.text }}</div>
          <div v-else>
            <input v-model="task.text" class="border rounded p-1" />
            <button @click="task.editing = false" class="ml-2 bg-green-500 text-white px-2 py-1 rounded">Uložit</button>
          </div>
          <div>
            <button @click="task.editing = true" class="text-yellow-500 mr-2">✏️</button>
            <button @click="generalTasks.splice(index, 1)" class="text-red-500">🗑️</button>
          </div>
        </li>
      </ul>
    </section>

    <!-- Přidání nového seznamu -->
    <div class="mb-6">
      <h2 class="text-lg font-semibold mb-2">Seznam úkolů</h2>
      <input v-model="newListName" placeholder="Název nového seznamu" class="border rounded p-2 mr-2" />
      <button @click="addList" class="bg-green-600 text-white px-4 py-2 rounded">Přidat seznam</button>
    </div>

    <!-- Výpis seznamů -->
    <div v-for="(list, listIndex) in lists" :key="list.id" class="border p-4 rounded mb-6 bg-gray-50">
      <div class="flex justify-between items-center mb-2">
        <div v-if="!list.editing">
          <h2 class="text-xl font-semibold">{{ list.name }}</h2>
        </div>
        <div v-else>
          <input v-model="list.name" class="border rounded p-1" />
        </div>
        <div>
          <button @click="list.editing = !list.editing" class="text-yellow-500 mr-2">✏️</button>
          <button @click="deleteList(listIndex)" class="text-red-500">🗑️</button>
        </div>
      </div>

      <!-- Přidávání úkolů -->
      <div class="mb-2">
        <input v-model="list.newTask" @keyup.enter="addTask(list)" placeholder="Nový úkol" class="border rounded p-2 mr-2" />
        <button @click="addTask(list)" class="bg-blue-600 text-white px-3 py-1 rounded">Přidat</button>
      </div>

      <!-- Úkoly v seznamu -->
      <ul>
        <li v-for="(task, taskIndex) in list.tasks" :key="taskIndex" class="flex justify-between items-center mb-1">
          <div v-if="!task.editing">{{ task.text }}</div>
          <div v-else>
            <input v-model="task.text" class="border rounded p-1" />
            <button @click="task.editing = false" class="ml-2 bg-green-500 text-white px-2 py-1 rounded">Uložit</button>
          </div>
          <div>
            <button @click="task.editing = true" class="text-yellow-500 mr-2">✏️</button>
            <button @click="list.tasks.splice(taskIndex, 1)" class="text-red-500">🗑️</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'

// Samostatné úkoly (bez seznamu)
const generalTasks = ref([])
const newGeneralTask = ref('')

// Seznamy
const lists = ref([])
const newListName = ref('')

// Samostatný úkol
function addGeneralTask() {
  if (!newGeneralTask.value.trim()) return
  generalTasks.value.push({ text: newGeneralTask.value, editing: false })
  newGeneralTask.value = ''
}

// Nový seznam
function addList() {
  if (!newListName.value.trim()) return
  lists.value.push({
    id: Date.now(),
    name: newListName.value,
    newTask: '',
    editing: false,
    tasks: []
  })
  newListName.value = ''
}

// Úkol do seznamu
function addTask(list) {
  if (!list.newTask.trim()) return
  list.tasks.push({ text: list.newTask, editing: false })
  list.newTask = ''
}

// Smazání seznamu
function deleteList(index) {
  if (confirm('Opravdu smazat tento seznam?')) {
    lists.value.splice(index, 1)
  }
}

// LocalStorage ukládání
watch([lists, generalTasks], () => {
  localStorage.setItem('lists', JSON.stringify(lists.value))
  localStorage.setItem('generalTasks', JSON.stringify(generalTasks.value))
}, { deep: true })

// Načtení při startu
onMounted(() => {
  const savedLists = localStorage.getItem('lists')
  if (savedLists) lists.value = JSON.parse(savedLists)

  const savedGeneral = localStorage.getItem('generalTasks')
  if (savedGeneral) generalTasks.value = JSON.parse(savedGeneral)
})

</script>

<style scoped>

</style>