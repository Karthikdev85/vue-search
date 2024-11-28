<template>
  <div
    @click="openModal"
    class="rounded-lg h-[340px] overflow-hidden shadow-md transition-all duration-300 transform hover:-translate-y-2 cursor-pointer"
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
      class="p-4 transition-colors flex flex-col justify-between h-[45%]"
      :class="{
        'bg-white text-gray-900': !darkMode,
        'bg-gray-800 text-gray-100': darkMode,
      }"
    >
      <div>
        <h3 class="text-lg font-semibold mb-2 truncate" :title="result.title">
          {{ result.title }}
        </h3>
        <p
          class="text-sm line-clamp-2"
          :class="{
            'text-gray-600': !darkMode,
            'text-gray-400': darkMode,
          }"
        >
          {{ result.snippet }}
        </p>
      </div>

      <div class="">
        <span
          class="text-sm"
          :class="{
            'text-gray-500': !darkMode,
            'text-gray-400': darkMode,
          }"
        >
          By {{ result.details.metadata.photographer }}
        </span>
      </div>
    </div>

    <!-- Modal -->
    <teleport to="body">
      <Modal
        v-if="isModalOpen"
        :result="result"
        :dark-mode="darkMode"
        @closeModal="closeModal"
      />
    </teleport>
  </div>
</template>

<script setup>
import Modal from "./Modal.vue";
import { ref } from "vue";

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

// Modal state
const isModalOpen = ref(false);

// Modal methods
const openModal = () => {
  isModalOpen.value = true;
};

const closeModal = () => {
  isModalOpen.value = false;
};
</script>
