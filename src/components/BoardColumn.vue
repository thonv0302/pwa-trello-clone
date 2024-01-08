<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
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

const router = useRouter()
const boardStore = useBoardStore()

function deleteColumn(columnIndex) {
  boardStore.deleteColumn(columnIndex)
}

function goToTask(taskId) {
  router.push({
    name: 'task',
    params: {
      id: taskId
    }
  })
}

const newTaskName = ref('')

function addTask() {
  boardStore.addTask({
    taskName: newTaskName.value,
    columnIndex: props.columnIndex
  })
  newTaskName.value = ''
}

const columnIndexDraging = ref(null)
const taskIndexDraging = ref(null)

function dropItem(event, { toColumnIndex, toTaskIndex }) {
  const type = event.dataTransfer.getData('type')
  const fromColumnIndex = event.dataTransfer.getData('from-column-index')

  if (type === 'task') {
    const fromTaskIndex = event.dataTransfer.getData('from-task-index')
    // if (
    //   (!checkFromTopToBottom.value &&
    //     taskIndexDraging.value === toTaskIndex &&
    //     columnIndexDraging.value === toColumnIndex) ||
    //   (checkFromTopToBottom.value &&
    //     currentTaskIndex.value === toTaskIndex &&
    //     columnIndexDraging.value === toColumnIndex)
    // ) {
    //   currentTaskIndex.value = null
    //   columnIndexDraging.value = null
    //   taskIndexDraging.value = null
    //   return
    // }
    boardStore.moveTask({
      fromTaskIndex,
      toTaskIndex: toTaskIndex,
      fromColumnIndex,
      toColumnIndex
    })

    // currentTaskIndex.value = null
    // columnIndexDraging.value = null
    // taskIndexDraging.value = null
  } else if (type === 'column') {
    boardStore.moveColumn({
      fromColumnIndex,
      toColumnIndex
    })
  }
}

function pickupColumn(event, fromColumnIndex) {
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.setData('type', 'column')
  event.dataTransfer.setData('from-column-index', fromColumnIndex)
}

function pickupTask(event, { fromColumnIndex, fromTaskIndex }) {
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.setData('type', 'task')
  event.dataTransfer.setData('from-column-index', fromColumnIndex)
  event.dataTransfer.setData('from-task-index', fromTaskIndex)
  columnIndexDraging.value = +fromColumnIndex
  taskIndexDraging.value = +fromTaskIndex
}

const currentTaskIndex = ref(null)
// const checkFromTopToBottom = ref(false)
const dragOverTask = (event, { toTaskIndex, toColumnIndex }) => {
  var closestLi = event.target.closest('li')

  const box = closestLi.getBoundingClientRect()
  const boxCenterY = box.y + box.height / 2
  console.log('closestLi: ', closestLi)
  // if (toColumnIndex !== columnIndexDraging.value || toTaskIndex !== taskIndexDraging.value) {
  //   if (event.clientY >= boxCenterY) {
  //     toTaskIndex++
  //   }

  //   if (toColumnIndex === columnIndexDraging.value) {
  //     checkFromTopToBottom.value = toTaskIndex > taskIndexDraging.value
  //   }

  //   currentTaskIndex.value = toTaskIndex
  // } else if (toTaskIndex === taskIndexDraging.value && event.clientY < boxCenterY) {
  //   currentTaskIndex.value = toTaskIndex
  // }
}

const dragLeaveDropZone = () => {
  currentTaskIndex.value = null
}
</script>

const
<template>
  <div
    class="bg-gray-200 p-5 rounded min-w-[250px]"
    :draggable="true"
    @dragstart.self="pickupColumn($event, columnIndex)"
    @dragenter.prevent
    @dragover.prevent
    @drop.stop="dropItem($event, { toColumnIndex: columnIndex })"
    @dragleave="dragLeaveDropZone"
  >
    <header>
      <input
        class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5 font-bold mb-4"
        type="text"
        v-model="column.name"
      />
      <button @click="deleteColumn(columnIndex)">x</button>
    </header>
    <TransitionGroup tag="ul" name="fade">
      <li
        v-for="(task, taskIndex) in column.tasks"
        :key="task.id"
        :draggable="true"
        @dragover.prevent.stop="
          dragOverTask($event, {
            toTaskIndex: taskIndex,
            toColumnIndex: columnIndex
          })
        "
        @dragstart="
          pickupTask($event, {
            fromColumnIndex: columnIndex,
            fromTaskIndex: taskIndex
          })
        "
        @drop.stop="
          dropItem($event, {
            toColumnIndex: columnIndex,
            toTaskIndex: taskIndex
          })
        "
      >
        <div
          :class="[
            'w-full h-[2px] bg-gray-200 my-1 transition-all',
            {
              'bg-red-400': taskIndex === currentTaskIndex
            }
          ]"
        ></div>
        <div class="task bg-white p-2 rounded shadow-sm max-w-[250px] flex">
          <div class="mb-4" @click="goToTask(task.id)">
            <span class="font-semibold">{{ task.name }}</span>
            <p>{{ task.description }}</p>
          </div>
        </div>
      </li>
      <li
        :class="[
          'w-full h-[2px] bg-gray-200 my-1 transition-all',
          {
            'bg-red-400': column.tasks.length === currentTaskIndex
          }
        ]"
      ></li>
    </TransitionGroup>
    <input
      class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5 font-bold mb-4"
      type="text"
      placeholder="Create new task"
      v-model="newTaskName"
      @keyup.enter="addTask"
    />
  </div>
</template>

<style>
.fade-move,
.fade-enter-active,
.fade-leave-active {
  transition: all 0.5s cubic-bezier(0.55, 0, 0.1, 1);
}

/* 2. declare enter from and leave to state */
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: scaleY(0.01) translate(30px, 0);
}

/* 3. ensure leaving items are taken out of layout flow so that moving
      animations can be calculated correctly. */
.fade-leave-active {
  position: absolute;
}
</style>
