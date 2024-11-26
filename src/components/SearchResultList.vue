<template>
  <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
    <SearchResultItem
      v-for="result in results"
      :key="result.id"
      :result="result"
      :dark-mode="darkMode"
      @click="$emit('item-click', result)"
    />
  </div>
</template>

<script setup>
import { defineProps } from "vue";
import SearchResultItem from "./SearchResultItem.vue";

// Define the props using defineProps
const props = defineProps({
  results: {
    type: Array,
    required: true,
  },
  darkMode: {
    type: Boolean,
    default: false,
  },
});
</script>
