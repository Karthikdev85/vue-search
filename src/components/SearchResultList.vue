<template>
  <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
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
import SearchResultItem from "./SearchResultItem.vue";

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
