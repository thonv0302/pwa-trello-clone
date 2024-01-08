<script setup>
import { useRoute, useRouter } from 'vue-router'
import BoardColumn from '@/components/trello/BoardColumn.vue'
import { useBoardStore } from '@/stores/boardStore'

import { ref, computed } from 'vue'
const route = useRoute()
const router = useRouter()
const boardStore = useBoardStore()
const newColumnName = ref('')

function addColumn() {
  boardStore.addColumn(newColumnName.value)
  newColumnName.value = ''
}

const isModalOpen = computed(() => {
  return route.name === 'task'
})

function closeModal() {
  router.push({
    name: 'board'
  })
}
</script>
<template>
  <TransitionGroup tag="div" name="fade" class="flex items-start overflow-x-auto gap-4">
    <BoardColumn
      v-for="(column, columnIndex) in boardStore.board.columns"
      :key="column.id"
      :column="column"
      :columnIndex="columnIndex"
    />

    <input
      key="add-column"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
      type="text"
      placeholder="+ Add Another Column"
      @keyup.enter="addColumn"
      v-model="newColumnName"
    />
  </TransitionGroup>

  <transition name="fade" :class="['absolute inset-0 z-40']">
    <router-view v-slot="{ Component }">
      <component :is="Component" />
    </router-view>
  </transition>
  <div v-if="isModalOpen" @click="closeModal">
    <div
      :class="[
        'fixed top-0 left-0 right-0 bottom-0 z-10',
        {
          'bg-black-50': isModalOpen
        }
      ]"
    ></div>
  </div>

  <!-- <pre>{{ boardStore.board }}</pre> -->
</template>

<style scoped>
.bg-black-50 {
  background-color: rgba(0, 0, 0, 0.3);
}
</style>
