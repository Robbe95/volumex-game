<script setup lang="ts">
interface Props {
  image: string
  description: string
}
const props = defineProps<Props>()
</script>

<template>
  <div class="w-40 flex flex-col gap-6  ">
    <img :src="props.image" alt="instruction icon">
    <p>{{ props.description }}</p>
  </div>
</template>
