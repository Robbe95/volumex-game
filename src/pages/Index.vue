<script setup lang="ts">
import { useHead } from '@vueuse/head'
enum CurrentPageMode {
  START = 'start',
  FORM = 'form',
  GAME = 'game',
  END = 'end',
}
useHead({
  title: 'Test',
  meta: [
    {
      name: 'description',
      content: 'Dit is een test component',
    },
  ],

})
const pageMode = ref(CurrentPageMode.START)
const gamePlaying = ref(false)

const setPageMode = (mode: CurrentPageMode) => {
  pageMode.value = mode
}
</script>

<template>
  <div class="flex items-center justify-center flex-col h-100vh max-w-100vw bg-#1A1724 overflow-hidden">
    <div class="overflow-hidden h-100vh w-100vw">
      <Transition name="fade" mode="out-in">
        <StartScreen v-if="pageMode === CurrentPageMode.START" class="overflow-hidden" @start-game="setPageMode(CurrentPageMode.GAME)" />
        <TruckGame v-else-if="pageMode === CurrentPageMode.GAME" />
      </Transition>
    </div>
    <div class="radial-background -translate-x-1/2 transform bottom-0 left-1/2 absolute w-1/2 h-1/2 " />
  </div>
</template>

<style scoped>
.radial-background {
  background: radial-gradient(47.69% 85.77% at 50% 100%, rgba(0, 80, 140, 0.3) 0%, rgba(0, 80, 140, 0) 100%) /* warning: gradient uses a rotation that is not supported by CSS and may not behave as expected */;

}
</style>
