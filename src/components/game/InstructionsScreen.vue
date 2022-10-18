<script setup lang="ts">
import gsap from 'gsap'
import BaseButton from '../BaseButton.vue'
import InstructionIconBlock from './instructions/InstructionIconBlock.vue'
import SwipeIcon from '@/assets/swipe-icon.svg'
import ConeIcon from '@/assets/cone-icon.svg'
import BoxIcon from '@/assets/box-icon.svg'
const emits = defineEmits(['next'])

const instructions = [
  {
    image: SwipeIcon,
    description: 'Bestuur de truck door te swipen',
  },
  {
    image: BoxIcon,
    description: 'Verzamel zo veel mogelijk pakketjes',
  },
  {
    image: ConeIcon,
    description: 'Vermijd de kegels!',
  },
]

const animateIconsBounce = () => {
  const icons = document.querySelectorAll('.icon')
  const tl = gsap.timeline({ repeat: -1 })
  tl.to(icons, { y: -10, duration: 0.5, stagger: 0.2 })
    .to(icons, { y: 0, duration: 0.5, stagger: 0.2 })
}

const animateIconsIntro = () => {
  const icons = document.querySelectorAll('.icon')
  const tl = gsap.timeline()
  tl.from([icons, '.start-button'], {
    opacity: 0,
    y: 0,
    scale: 0,
    ease: 'back.out(1.7)',
    stagger: 0.3,
    duration: 0.5,
  }).add(() => animateIconsBounce())
}

onMounted(() => {
  animateIconsIntro()
})
</script>

<template>
  <section class="py-40 w-full h-full flex flex-col justify-between items-center text-center text-white z-50">
    <div class="flex flex-col gap-8">
      <h1 class="title">
        Instructies
      </h1>
      <p class="w-prose">
        Je krijgt 1 minuut om zoveel mogelijk pakketjes op te pikken met je Volumex truck. Let op voor de kegels, hierdoor verlies je pakketjes!
      </p>
    </div>
    <article class="flex gap-32">
      <InstructionIconBlock v-for="instruction in instructions" v-bind="instruction" :key="instruction.description" class="icon" />
    </article>
    <div class="start-button">
      <BaseButton class="w-full" @click="emits('next')">
        Start
      </BaseButton>
    </div>
  </section>
</template>
