<template>
  <div @drop.stop="onDrop" @dragover.prevent @dragenter.prevent><slot /></div>
</template>

<script setup>
const emits = defineEmits(['drop'])
const onDrop = (e) => {
  const transferData = JSON.parse(e.dataTransfer.getData('payload'))
  emits('drop', transferData)
}
</script>
