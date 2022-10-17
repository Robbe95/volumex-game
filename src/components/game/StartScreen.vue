<script setup lang="ts">
import gsap from 'gsap'

onMounted(() => {
  const transitionInTl = gsap.timeline({
    duration: 2,
  })
  const movementTl = gsap.timeline(
    {
      duration: 2,
      repeat: -1,
    },
  )

  transitionInTl.fromTo(
    '.truck',
    {
      opacity: 0,
      y: '100%',
    },
    {
      opacity: 1,
      y: '50%',
      duration: 2,

    },
  )

  movementTl
    .to(
      '.truck-wrapper',
      {
        x: gsap.utils.random(-20, 0),
        y: gsap.utils.random(0, 10),
      })
    .to(
      '.truck-wrapper',
      {
        x: gsap.utils.random(0, 20),
        y: gsap.utils.random(10, 20),
        rotate: gsap.utils.random(0, 5),
      })
    .to(
      '.truck-wrapper',
      {
        x: 0,
        y: 0,
        rotate: 0,
      })
})
</script>

<template>
  <div class="w-full flex flex-row items-center justify-center">
    <div class="btn btn-primary max-w-max">
      Win stuff
    </div>
    <div class="truck-wrapper absolute bottom-0">
      <img class="truck" src="@/assets/full-truck.png">
    </div>
  </div>
</template>
