<script setup lang="ts">
import { useHead } from '@vueuse/head'
useHead({
  title: 'Test',
  meta: [
    {
      name: 'description',
      content: 'Dit is een test component',
    },
  ],

})

const gamePlaying = ref(false)
</script>

<template>
  <div class="flex items-center justify-center flex-col h-100vh w-100vw bg-#1A1724 overflow-hidden">
    <StartScreen />
    <TruckGame v-if="gamePlaying" />
    <div class="radial-background -translate-x-1/2 transform bottom-0 left-1/2 absolute w-1/2 h-1/2 " />
  </div>
</template>

<style scoped>
.radial-background {
  background: radial-gradient(47.69% 85.77% at 50% 100%, rgba(0, 80, 140, 0.3) 0%, rgba(0, 80, 140, 0) 100%) /* warning: gradient uses a rotation that is not supported by CSS and may not behave as expected */;

}
</style>
