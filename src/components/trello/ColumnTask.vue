<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import AppDrag from '../AppDrag.vue'
import AppDrop from '../AppDrop.vue'
import { useBoardStore } from '@/stores/boardStore'

const props = defineProps({
  task: {
    type: Object,
    required: true
  },
  taskIndex: {
    type: Number,
    required: true
  },
  columnIndex: {
    type: Number
  }
})

const router = useRouter()
const boardStore = useBoardStore()

function moveTaskOrColumn(transferData) {
  if (transferData.type === 'task') {
    moveTask(transferData)
  }
}

const isDifferentColumn = ref(false)
function moveTask({ fromColumnIndex, fromTaskIndex }) {
  if (fromColumnIndex !== props.columnIndex) {
    isDifferentColumn.value = true
  } else {
    isDifferentColumn.value = false
  }
  boardStore.moveTask({
    fromTaskIndex,
    toTaskIndex: props.taskIndex,
    fromColumnIndex,
    toColumnIndex: props.columnIndex
  })
}

function goToTask(taskId) {
  router.push({
    name: 'task',
    params: {
      id: taskId
    }
  })
}
defineExpose({
  isDifferentColumn
})
</script>

<template>
  <li class="w-full">
    <AppDrop @drop="moveTaskOrColumn">
      <AppDrag
        :transfer-data="{
          type: 'task',
          fromColumnIndex: columnIndex,
          fromTaskIndex: taskIndex
        }"
      >
        <div class="task bg-white p-2 rounded shadow-sm w-full" @click="goToTask(task.id)">
          <div class="font-semibold mb-1">{{ task.name }}</div>
          <p>{{ task.description }}</p>
        </div>
      </AppDrag>
    </AppDrop>
  </li>
</template>
