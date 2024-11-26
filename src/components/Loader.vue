<template>
  <div class="flex justify-center items-center">
    <div
      class="w-12 h-12 border-4 rounded-full animate-spin"
      :class="{
        'border-blue-200 border-t-blue-600': !darkMode,
        'border-gray-700 border-t-gray-500': darkMode,
      }"
    ></div>
  </div>
</template>

<script setup>
import { defineProps } from "vue";

const props = defineProps({
  darkMode: {
    type: Boolean,
    default: false,
  },
});
</script>
