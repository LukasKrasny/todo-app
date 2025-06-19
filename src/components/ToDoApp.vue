<template>
  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
    <!-- Jednotlivé úkoly -->
    <section class="p-4 border rounded bg-white dark:bg-gray-800 dark:border-gray-700 shadow">
      <h2 class="text-lg sm:text-xl font-semibold mb-4 text-black dark:text-white">Jednotlivé úkoly</h2>

      <!-- Input + tlačítko -->
      <div class="flex flex-col sm:flex-row gap-2 items-stretch mb-4">
        <input v-model="newGeneralTask" placeholder="Zadej úkol"
          class="w-full border rounded p-2 bg-white dark:bg-gray-900 text-black dark:text-white placeholder-gray-500 dark:placeholder-gray-400 border-gray-300 dark:border-gray-600" />
        <button @click="addGeneralTask"
          class="w-full sm:w-auto h-full bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded transition">
          Přidat
        </button>
      </div>

      <!-- Výpis úkolů -->
      <ul class="space-y-2">
        <transition-group name="fade" tag="ul" class="space-y-2">
          <li v-for="(task, index) in generalTasks" :key="index"
            class="flex justify-between items-center p-2 rounded bg-white dark:bg-gray-700 border border-gray-300 dark:border-gray-600">
            <div class="flex items-center gap-2 grow">
              <input type="checkbox" v-model="task.done" />
              <span v-if="!task.editing" :class="task.done ? 'line-through text-gray-400' : 'text-black dark:text-white'"
              class="truncate">
                {{ task.text }}
              </span>
              <input 
                v-else v-model="task.text" 
                @blur="task.editing = false" 
                @keyup.enter="task.editing = false"
                class="bg-white dark:bg-gray-900 text-black dark:text-white px-1 py-0.5 rounded w-full max-w-[calc(100%-3rem)]" />
            </div>
            <div class="flex gap-2 shrink-0">
              <button @click="task.editing = true" class="text-yellow-500">✏️</button>
              <button @click="generalTasks.splice(index, 1)" class="text-red-500">🗑️</button>
            </div>
          </li>
        </transition-group>
      </ul>
    </section>

    <!-- Seznamy úkolů -->
    <section class="space-y-6">
      <!-- Přidání nového seznamu -->
      <div class="flex flex-col sm:flex-row gap-2 items-stretch mb-4">
        <input v-model="newListName" placeholder="Název nového seznamu"
          class="w-full border rounded p-2 bg-white dark:bg-gray-900 text-black dark:text-white placeholder-gray-500 dark:placeholder-gray-400 border-gray-300 dark:border-gray-600" />
        <button @click="addList"
          class="w-full sm:w-auto h-full bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded transition">
          Přidat seznam
        </button>
      </div>

      <!-- Výpis seznamů -->
      <transition-group name="fade" tag="ul" class="space-y-2">
        <div v-for="(list, listIndex) in lists" :key="list.id"
          class="p-4 border rounded bg-white dark:bg-gray-800 dark:border-gray-700 shadow space-y-4">
          <div class="flex justify-between items-center">
            <div v-if="!list.editing">
              <h2 class="text-lg font-semibold text-black dark:text-white">{{ list.name }}</h2>
            </div>
            <div v-else>
              <input v-model="list.name"
                class="bg-white dark:bg-gray-900 text-black dark:text-white px-1 py-1 rounded w-full max-w-[calc(100%-3rem)]" />
            </div>
            <div class="flex gap-2 shrink-0">
              <button @click="list.editing = !list.editing" class="text-yellow-500">✏️</button>
              <button @click="deleteList(listIndex)" class="text-red-500">🗑️</button>
            </div>
          </div>

          <div class="flex flex-col sm:flex-row gap-2 items-stretch mb-4">
            <input v-model="list.newTask" @keyup.enter="addTask(list)" placeholder="Nový úkol"
              class="w-full border rounded p-2 bg-white dark:bg-gray-900 text-black dark:text-white placeholder-gray-500 dark:placeholder-gray-400 border-gray-300 dark:border-gray-600" />
            <button @click="addTask(list)"
              class="w-full sm:w-auto h-full bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded transition">
              Přidat
            </button>
          </div>

          <!-- Výpis úkolů v seznamu -->
          <ul class="space-y-2">
            <transition-group name="fade" tag="ul" class="space-y-2">
              <li v-for="(task, taskIndex) in list.tasks" :key="taskIndex"
                class="flex justify-between items-center p-2 rounded bg-white dark:bg-gray-700 text-black dark:text-white border border-gray-300 dark:border-gray-600">
                <div class="flex items-center gap-2 grow">
                  <input type="checkbox" v-model="task.done" />
                  <span v-if="!task.editing" :class="task.done ? 'line-through text-gray-400' : 'text-black dark:text-white'">
                    {{ task.text }}
                  </span>
                  <input 
                    v-else v-model="task.text" 
                    @blur="task.editing = false" 
                    @keyup.enter="task.editing = false" 
                    class="bg-white dark:bg-gray-900 text-black dark:text-white px-1 py-1 rounded w-full max-w-[calc(100%-3rem)]" />
                </div>
                <div class="flex gap-2 shrink-0">
                  <button @click="task.editing = true" class="text-yellow-500">✏️</button>
                  <button @click="list.tasks.splice(taskIndex, 1)" class="text-red-500">🗑️</button>
                </div>
              </li>
            </transition-group>
          </ul>
        </div>
      </transition-group>
    </section>
  </div>
</template>


<script setup>
import { ref, watch, onMounted } from 'vue';
import draggable from 'vuedraggable'

// Jednotlivé úkoly (bez seznamu)
const generalTasks = ref([])
const newGeneralTask = ref('')

// Seznamy
const lists = ref([])
const newListName = ref('')

// Jenotlivý úkol
function addGeneralTask() {
  if (!newGeneralTask.value.trim()) return
  generalTasks.value.push({ text: newGeneralTask.value, editing: false, done: false })
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
  list.tasks.push({ text: list.newTask, editing: false, done: false })
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
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}
</style>