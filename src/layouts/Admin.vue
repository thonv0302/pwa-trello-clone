<script setup>
import TheHeader from '@/components/admin/TheHeader.vue'
import TheSidebar from '@/components/admin/TheSidebar.vue'
</script>
<template>
  <div>
    <TheSidebar />
    <div class="lg:pl-64 flex flex-col flex-1">
      <main class="flex-1">
        <TheHeader />
        <main class="flex-1">
          <div class="py-6">
            <div class="max-w-7xl mx-auto px-3 sm:px-6 md:px-8">
              <slot />
            </div>
          </div>
        </main>
      </main>
    </div>
  </div>
</template>
