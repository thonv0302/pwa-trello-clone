<template>
  <AppDrop @drop="moveTaskOrColumn">
    <AppDrag
      class="bg-gray-200 p-5 rounded min-w-[250px]"
      :transfer-data="{
        type: 'column',
        fromColumnIndex: columnIndex
      }"
    >
      <header class="mb-2">
        <input
          @dragstart.prevent
          class="bg-transparent focus:bg-white rounded py-1 px-2 font-bold"
          type="text"
          v-model="columnName"
          @keyup.enter="changeColumnTitle"
        />
        <button @click="deleteColumn" class="ms-1 p-1"><XMarkIcon class="w-4 h-4" /></button>
      </header>

      <TransitionGroup tag="ul" name="list">
        <ColumnTask
          ref="taskComp"
          v-for="(task, taskIndex) in column.tasks"
          :key="task.id"
          :task="task"
          :task-index="taskIndex"
          :columnIndex="columnIndexClone"
          class="mb-2"
        />
      </TransitionGroup>
      <input
        class="bg-transparent focus:bg-white rounded py-1 px-2 font-bold mb-4 w-full"
        type="text"
        placeholder="Create new task"
        v-model="newTaskName"
        @keyup.enter="addTask"
      />
    </AppDrag>
  </AppDrop>
</template>

<script setup>
import { ref } from 'vue'
import { XMarkIcon } from '@heroicons/vue/24/solid'
import AppDrag from '../AppDrag.vue'
import AppDrop from '../AppDrop.vue'
import ColumnTask from './ColumnTask.vue'
import { useBoardStore } from '@/stores/boardStore'

const props = defineProps({
  column: {
    type: Object,
    required: true
  },
  columnIndex: {
    type: Number,
    required: true
  }
})

const boardStore = useBoardStore()
const columnIndexClone = ref(props.columnIndex)
const columnName = ref(props.column.name)
const newTaskName = ref('')

const taskComp = ref()

function moveTaskOrColumn(transferData) {
  if (transferData.type === 'column') {
    moveColumn(transferData)
  } else if (transferData.type === 'task') {
    moveTask(transferData)
  }
}

function moveColumn({ fromColumnIndex }) {
  boardStore.moveColumn({
    fromColumnIndex,
    toColumnIndex: props.columnIndex
  })
}

function moveTask({ fromColumnIndex, fromTaskIndex }) {
  boardStore.moveTask({
    fromTaskIndex,
    fromColumnIndex,
    toColumnIndex: props.columnIndex
  })
}

function addTask() {
  boardStore.addTask({
    taskName: newTaskName.value,
    columnIndex: props.columnIndex
  })
  newTaskName.value = ''
}

function changeColumnTitle() {
  boardStore.changeColumnTitle({
    newColumnName: columnName.value,
    columnIndex: props.columnIndex
  })
}

function deleteColumn() {
  boardStore.deleteColumn(props.columnIndex)
}
</script>

<style lang="css">
.list-move, /* apply transition to moving elements */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translate(10px);
}

/* ensure leaving items are taken out of layout flow so that moving
   animations can be calculated correctly. */
.list-leave-active {
  /* position: absolute; */
}
</style>
