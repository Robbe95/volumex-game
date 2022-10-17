<script setup lang="ts">
const emits = defineEmits(['click'])

const onClick = () => {
  emits('click')
}
</script>

<template>
  <button class="px-8 py-4 bg-#E00A4D rounded-xl max-w-max button-shadow hover:translate-y-4px transition-all" @click="onClick">
    <slot />
  </button>
</template>

<style scoped>
.button-shadow {
  box-shadow: 0px 4px 0px #9B123D, 0px 8px 20px rgba(0, 0, 0, 0.6);
}

.button-shadow:hover {
  box-shadow: none;
}
</style>
