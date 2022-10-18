<script setup lang="ts">
import { useHead } from '@vueuse/head'
import gsap from 'gsap'

enum CurrentPageMode {
  START = 'start',
  FORM = 'form',
  INSTRUCTIONS = 'instructions',

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
/* Road movement */
const roadTl = gsap.timeline(
  {
    repeat: -1,
    ease: 'none',
  },
)

const roadMovement = () => {
  roadTl
    .to(
      '.road',
      {
        y: '50vh',
        ease: 'none',
        duration: 2,
      },
    )
}

onMounted(() => {
  roadMovement()
})

const pageMode = ref(CurrentPageMode.START)
const gamePlaying = ref(false)

const setPageMode = (mode: CurrentPageMode) => {
  pageMode.value = mode
}
</script>

<template>
  <div class="flex items-center justify-center flex-col h-100vh max-w-100vw bg-#1A1724 overflow-hidden">
    <div class="overflow-hidden h-100vh w-100vw z-20">
      <Transition name="fade" mode="out-in">
        <StartScreen v-if="pageMode === CurrentPageMode.START" class="overflow-hidden" @start-game="setPageMode(CurrentPageMode.FORM)" />
        <FormScreen v-else-if="pageMode === CurrentPageMode.FORM" class="overflow-hidden" @next="setPageMode(CurrentPageMode.INSTRUCTIONS)" />
        <InstructionsScreen v-else-if="pageMode === CurrentPageMode.INSTRUCTIONS" class="overflow-hidden" @next="setPageMode(CurrentPageMode.GAME)" />
        <TruckGame v-else-if="pageMode === CurrentPageMode.GAME" />
      </Transition>
    </div>
    <div class="road w-auto -translate-y-50vh absolute left-1/2 -translate-x-1/2 transform z-0" :class="[pageMode === CurrentPageMode.GAME ? 'opacity-0' : 'opacity-100']">
      <img class="h-screen " src="@/assets/street.svg">
      <img class="h-screen" src="@/assets/street.svg">
    </div>
    <div class="radial-background -translate-x-1/2 transform bottom-0 left-1/2 absolute w-1/2 h-1/2 " />
  </div>
</template>

<style scoped>
.radial-background {
  background: radial-gradient(47.69% 85.77% at 50% 100%, rgba(0, 80, 140, 0.3) 0%, rgba(0, 80, 140, 0) 100%) /* warning: gradient uses a rotation that is not supported by CSS and may not behave as expected */;
}
</style>
