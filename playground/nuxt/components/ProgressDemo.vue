<script setup lang="ts">
const progressValue = ref(66)
</script>

<template>
  <div class="absolute text-sm left-4 top-3">
    <p>{{ progressValue.toFixed(2) }}</p>
  </div>
  <ProgressRoot
    v-model="progressValue"
    class="relative overflow-hidden bg-blackA9 rounded-full w-[300px] h-[25px]"
    style="transform: translateZ(0)"
  >
    <ProgressIndicator
      class="bg-white w-full h-full transition-transform duration-[660ms] ease-[cubic-bezier(0.65, 0, 0.35, 1)]"
      :style="`transform: translateX(-${100 - progressValue}%)`"
    />
  </ProgressRoot>
</template>
