<script setup lang="ts">
</script>

<template>
  <router-view />
</template>

<style>
@font-face {
  font-family: "Jakarta";
  src: url(@/assets/fonts/PlusJakarta/PlusJakartaSans-Regular.ttf)
    format("truetype");
}

@font-face {
  font-family: "Jakarta";
  src: url(@/assets/fonts/PlusJakarta/PlusJakartaSans-Medium.ttf)
    format("truetype");
  font-weight: 400;
}

@font-face {
  font-family: "Jakarta";
  src: url(@/assets/fonts/PlusJakarta/PlusJakartaSans-Bold.ttf)
    format("truetype");
  font-weight: 700;
}

@font-face {
  font-family: "Jakarta";
  src: url(@/assets/fonts/PlusJakarta/PlusJakartaSans-ExtraBold.ttf)
    format("truetype");
  font-weight: 800;
}

* {
  font-family: "Jakarta", sans-serif;
}

.fade-enter-active {
  transition: all 0.3s ease-out;
}

.fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
