<script setup lang="ts">
import gsap from 'gsap'
interface Props {
  score: number
}
const props = defineProps<Props>()
const emits = defineEmits(['restart'])

let counter = 0
let delay = 10
const x = props.score / 2
const countedUp = ref(0)

const countUp = () => {
  countedUp.value++
  delay = ((x - countedUp.value) * 4) + 100
  counter++
  if (counter < props.score) {
    setTimeout(() => {
      countUp()
    }, delay)
  }
}

countUp()
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

const restart = () => {
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
    ).add(() => emits('restart'))
}

onMounted(() => {
  truckMovingIn()
  truckRoadMovement()
})
</script>

<template>
  <div class="flex flex-row items-center justify-center overflow-hidden relative h-full w-full">
    <section class="z-20 text-white text-center flex flex-col gap-8 start-info mb-40 xl:mb-0 justify-center items-center">
      <h1 class="title flex text-center">
        <div>
          Je hebt
        </div>
        <div class="text-#00DEFF min-w-26">
          {{ countedUp }}
        </div>
        <div>
          pakketjes opgehaald.
        </div>
      </h1>
      <p class="max-w-60ch">
        Dit plaatst jou voorlopig op de 6de plaats! Goed gedaan!
      </p>
      <div>
        <BaseButton @click="restart">
          Opnieuw proberen?
        </BaseButton>
      </div>
    </section>
    <div class="absolute bottom-0 z-20 pointer-events-none left-1/2 -translate-x-1/2 w-full xl:w-auto">
      <div class="truck-wrapper will-change-transform w-full">
        <img class="truck will-change-transform" src="@/assets/full-truck.png">
      </div>
    </div>
  </div>
</template>

