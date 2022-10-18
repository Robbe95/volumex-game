<script setup lang="ts">
import gsap from 'gsap'
const emits = defineEmits(['startGame'])
/* Setup global timelines */
const transitionInTl = gsap.timeline({
  duration: 1,
})
const movementTl = gsap.timeline(
  {
    duration: 2,
    repeat: -1,
    repeatRefresh: true,
  },
)

const startGameTl = gsap.timeline()

/* Setup animation functions */
const truckMovingIn = () => {
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
}

const truckRoadMovement = () => {
  movementTl
    .to(
      '.truck-wrapper',
      {
        x: () => gsap.utils.random(-20, 0),
        y: () => gsap.utils.random(0, 10),
      })
    .to(
      '.truck-wrapper',
      {
        x: () => gsap.utils.random(0, 20),
        y: () => gsap.utils.random(10, 20),
        rotate: () => gsap.utils.random(-5, 5),
      })
    .to(
      '.truck-wrapper',
      {
        x: 0,
        y: 0,
        rotate: 0,
      },
    )
}

const startGame = () => {
  movementTl.pause()
  startGameTl.to(
    '.truck-wrapper',
    {
      x: 0,
      y: 0,
      rotate: 0,
    },
  ).to(
    '.start-info',
    {
      opacity: 0,
    },
    '<',
  )
    .to(
      '.truck-wrapper',
      {
        y: '80',
        duration: 1,
      },
    ).to(
      '.truck-wrapper',
      {
        y: '-200vh',
        duration: 2,
        ease: 'power1.in',
      },
    ).add(() => emits('startGame'))
}

onMounted(() => {
  truckMovingIn()
  truckRoadMovement()
})
</script>

<template>
  <div class="w-full flex flex-row items-center justify-center overflow-hidden relative h-full w-full">
    <section class="z-20 text-white text-center flex flex-col gap-8 start-info">
      <h1 class="title">
        Win jij duo tickets?
      </h1>
      <p class="max-w-60ch">
        Speel de beste score en win duo tickets naar de Volumex Belgian Truck Grand Prix 2023!
      </p>
      <div>
        <BaseButton @click="startGame">
          Start het spel
        </BaseButton>
      </div>
    </section>
    <div class="truck-wrapper absolute bottom-0 z-20 pointer-events-none">
      <img class="truck" src="@/assets/full-truck.png">
    </div>
  </div>
</template>

