<script setup lang="ts">
import BaseButton from '../BaseButton.vue'
const emits = defineEmits(['next'])
</script>

<template>
  <section class="py-40 w-full h-full flex flex-col justify-between items-center text-center text-white z-50">
    <div class="flex flex-col gap-8">
      <h1 class="title">
        Ready. Set. Go!
      </h1>
      <p class="w-prose">
        Voor we beginnen, mogen we je nog wat vragen?
      </p>
    </div>
    <div>
      <BaseButton class="w-full" @click="emits('next')">
        Volgende
      </BaseButton>
    </div>
  </section>
</template>
