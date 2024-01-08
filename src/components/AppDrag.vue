<template>
  <div
    class="select-none"
    :draggable="true"
    @dragstart.self="onDrag"
    @dragover.prevent
    @dragenter.prevent
  >
    <!-- :draggable="isDraging" -->
    <!-- @dragleave="isDraging = false" -->
    <!-- <div class="flex items-center my-1">
      <ArrowsPointingOutIcon
        class="w-5 h-5 cursor-move"
        @mousedown="isDraging = true"
      ></ArrowsPointingOutIcon>
      <slot name="title"></slot>
    </div> -->
    <slot></slot>
  </div>
</template>
<script setup>
const props = defineProps({
  transferData: {
    type: Object,
    required: true
  }
})

// const isDraging = ref(false)

const onDrag = (e) => {
  e.dataTransfer.effectAllowed = 'move'
  e.dataTransfer.dropEffect = 'move'
  e.dataTransfer.setData('payload', JSON.stringify(props.transferData))
}
</script>

<style scoped>
.line {
  width: 100%;
  height: 1px;
  background-color: brown;
}
</style>
