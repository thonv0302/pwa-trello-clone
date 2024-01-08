<script setup>
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { XMarkIcon, TrashIcon } from '@heroicons/vue/24/solid'

import { useBoardStore } from '@/stores/boardStore'

const boardStore = useBoardStore()
const route = useRoute()
const router = useRouter()

const task = computed(() => {
  return boardStore.getTask(route.params.id)
})

function deleteTask() {
  boardStore.deleteTask(route.params.id)
  router.push({
    name: 'board'
  })
}
</script>

<template>
  <div v-if="task" class="max-w-2xl bg-gray-200 my-96 mx-auto p-4 rounded h-[350px]">
    <header class="flex justify-between mb-4">
      <h2 class="font-bold text-lg">Edit {{ task.name }}</h2>
      <button
        @click="
          router.push({
            name: 'board'
          })
        "
        class="p-2"
      >
        <XMarkIcon class="w-4 h-4" />
      </button>
    </header>
    <main class="mb-auto">
      <div class="flex flex-col flex-grow items-start justify-between">
        <div class="flex justify-between w-full items-center mb-4">
          <input
            class="focus:bg-white rounded py-1 px-2 font-semibold w-full"
            v-model="task.name"
          />
        </div>
        <textarea class="w-full mb-4 py-1 px-2 rounded" rows="5" v-model="task.description">
        </textarea>
      </div>
    </main>

    <footer class="mt-auto">
      <button class="py-2 px-3 bg-rose-500 rounded text-white" @click="deleteTask">
        <TrashIcon class="w-4 h-4 inline-block me-2" />Delete
      </button>
    </footer>
  </div>
</template>
