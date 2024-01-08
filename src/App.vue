<template>
  <component :is="layoutSelected">
    <RouterView />
  </component>
</template>

<script setup>
import Auth from './layouts/Auth.vue'
import Admin from './layouts/Admin.vue'
import { computed } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const layoutSelected = computed(() => {
  if (['signUp', 'signIn'].includes(route.name)) {
    return Auth
  }

  return Admin
})
</script>
