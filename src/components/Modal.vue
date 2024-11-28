<template>
  <div
    class="fixed inset-0 z-50 flex items-center justify-center bg-[#161616] bg-opacity-50 p-4"
    @click.self="$emit('closeModal')"
  >
    <div
      class="relative max-w-4xl w-full max-h-[90vh] overflow-y-auto rounded-lg shadow-2xl transform transition-all duration-300 scale-100"
      :class="{
        'bg-white': !darkMode,
        'bg-gray-900': darkMode,
      }"
    >
      <!-- Close Button -->
      <button
        @click="$emit('closeModal')"
        class="absolute top-2 right-2 z-60 rounded-full p-1 transition-colors"
        :class="{
          'bg-gray-200 text-gray-800 hover:bg-gray-300': !darkMode,
          'bg-gray-700 text-gray-100 hover:bg-gray-600': darkMode,
        }"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M6 18L18 6M6 6l12 12"
          />
        </svg>
      </button>

      <!-- Modal Content -->
      <div class="grid md:grid-cols-2 gap-6 p-6">
        <!-- Image -->
        <div class="md:h-[400px]">
          <img
            :src="result.imageUrl"
            :alt="result.title"
            class="w-full h-full object-cover rounded-lg shadow-lg"
          />
        </div>

        <!-- Details -->
        <div>
          <h2
            class="text-2xl font-bold mb-4"
            :class="{
              'text-gray-900': !darkMode,
              'text-gray-100': darkMode,
            }"
          >
            {{ result.title }}
          </h2>

          <div
            class="space-y-4"
            :class="{
              'text-gray-700': !darkMode,
              'text-gray-300': darkMode,
            }"
          >
            <p>{{ result.details.fullDescription }}</p>

            <div class="grid grid-cols-2 gap-4">
              <div>
                <strong>Photographer:</strong>
                <p>{{ result.details.metadata.photographer }}</p>
              </div>
              <div>
                <strong>Created At:</strong>
                <p>{{ result.details.metadata.createdAt }}</p>
              </div>
              <div>
                <strong>Likes:</strong>
                <p>{{ result.details.metadata.likes }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
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
