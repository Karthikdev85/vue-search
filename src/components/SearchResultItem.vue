<template>
  <div
    class="rounded-lg overflow-hidden shadow-md transition-all duration-300 transform hover:-translate-y-2"
    :class="{
      'bg-white': !darkMode,
      'bg-gray-800': darkMode,
    }"
  >
    <img
      :src="result.imageUrl"
      :alt="result.title"
      class="w-full h-48 object-cover"
    />
    <div
      class="p-4 transition-colors"
      :class="{
        'bg-white text-gray-900': !darkMode,
        'bg-gray-800 text-gray-100': darkMode,
      }"
    >
      <h3 class="text-lg font-semibold mb-2 truncate" :title="result.title">
        {{ result.title }}
      </h3>
      <p
        class="text-sm mb-4 line-clamp-2"
        :class="{
          'text-gray-600': !darkMode,
          'text-gray-400': darkMode,
        }"
      >
        {{ result.snippet }}
      </p>

      <div class="flex justify-between items-center">
        <a
          :href="result.details.metadata.downloadLink"
          target="_blank"
          rel="noopener noreferrer"
          class="px-3 py-1 rounded-full text-sm transition-colors"
          :class="{
            'bg-blue-500 text-white hover:bg-blue-600': !darkMode,
            'bg-blue-700 text-gray-100 hover:bg-blue-800': darkMode,
          }"
        >
          Download
        </a>
        <span
          class="text-xs"
          :class="{
            'text-gray-500': !darkMode,
            'text-gray-400': darkMode,
          }"
        >
          By {{ result.details.metadata.photographer }}
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from "vue";

const props = defineProps({
  result: {
    type: Object,
    required: true,
  },
  darkMode: {
    type: Boolean,
    default: false,
  },
});
</script>
