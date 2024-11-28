<template>
  <div class="relative">
    <div
      class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none"
    >
      <svg
        class="h-5 w-5 transition-colors"
        :class="{
          'text-gray-400': !darkMode,
          'text-gray-500': darkMode,
        }"
        fill="currentColor"
        viewBox="0 0 20 20"
      >
        <path
          fill-rule="evenodd"
          d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
          clip-rule="evenodd"
        />
      </svg>
    </div>
    <input
      type="text"
      v-model="searchQuery"
      @input="debounceSearch"
      placeholder="Search images..."
      class="w-full pl-10 pr-4 py-2 rounded-full shadow-md outline-none focus:ring-2 transition-all duration-100"
      :class="{
        'bg-white border-gray-300 text-gray-900 focus:ring-blue-500': !darkMode,
        'bg-gray-700 border-gray-700 text-gray-100 focus:ring-blue-700':
          darkMode,
      }"
    />
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

const props = defineProps({
  darkMode: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(["search"]);

const searchQuery = ref("");
let debounceTimer = null;

const debounceSearch = () => {
  clearTimeout(debounceTimer);
  debounceTimer = setTimeout(() => {
    if (searchQuery.value.trim()) {
      emit("search", searchQuery.value);
    }
  }, 500);
};

watch(searchQuery, debounceSearch);
</script>
