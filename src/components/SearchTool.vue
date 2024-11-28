<template>
  <div
    class="min-h-screen transition-colors duration-300"
    :class="{
      'bg-gray-100 text-gray-900': !isDarkMode,
      'bg-gray-900 text-gray-100': isDarkMode,
    }"
  >
    <div class="max-w-5xl mx-auto px-4 py-8">
      <div class="flex justify-between items-center mb-6">
        <h1
          class="text-2xl font-bold transition-colors"
          :class="{
            'text-gray-900': !isDarkMode,
            'text-gray-100': isDarkMode,
          }"
        >
          Unsplash Image Search
        </h1>
        <button
          @click="toggleDarkMode"
          class="p-2 rounded-full transition-colors"
          :class="{
            'bg-gray-200 text-gray-800': !isDarkMode,
            'bg-gray-700 text-gray-100': isDarkMode,
          }"
        >
          {{ isDarkMode ? "☀️ Light" : "🌙 Dark" }}
        </button>
      </div>

      <SearchBar @search="performSearch" :dark-mode="isDarkMode" />

      <div class="mt-6">
        <transition-group name="results-list" tag="div">
          <div v-if="isLoading" key="loader" class="flex justify-center">
            <Loader :dark-mode="isDarkMode" />
          </div>

          <SearchResultList
            v-else-if="searchResults.length"
            key="results"
            :results="searchResults"
            :dark-mode="isDarkMode"
            @item-click="handleResultClick"
          />

          <div
            v-else-if="searchPerformed"
            key="no-results"
            class="text-center transition-colors"
            :class="{
              'text-gray-500': !isDarkMode,
              'text-gray-400': isDarkMode,
            }"
          >
            <p>No results found</p>
          </div>
        </transition-group>
        <!-- 
        <div
          v-if="searchResults.length && hasMoreResults"
          class="text-center mt-6"
        >
          <button
            @click="loadMoreResults"
            :disabled="isLoading"
            class="px-4 py-2 rounded-full transition-all duration-300"
            :class="{
              'bg-blue-500 text-white hover:bg-blue-600': !isDarkMode,
              'bg-blue-700 text-gray-100 hover:bg-blue-800': isDarkMode,
              'opacity-50 cursor-not-allowed': isLoading,
            }"
          >
            {{ isLoading ? "Loading..." : "Load More" }}
          </button>
        </div> -->
        <div
          v-if="searchResults.length && hasMoreResults"
          class="text-center mt-6"
        >
          <button
            v-if="currentPage > 1"
            @click="loadMoreResults('prev')"
            :disabled="isLoading"
            class="px-4 py-2 rounded-full transition-all duration-300"
            :class="{
              'bg-blue-500 text-white hover:bg-blue-600': !isDarkMode,
              'bg-blue-700 text-gray-100 hover:bg-blue-800': isDarkMode,
              'opacity-50 cursor-not-allowed': isLoading,
            }"
          >
            Prev
          </button>
          <button
            v-if="currentPage < totalPages"
            @click="loadMoreResults('next')"
            :disabled="isLoading"
            class="px-4 py-2 rounded-full transition-all duration-300"
            :class="{
              'bg-blue-500 text-white hover:bg-blue-600': !isDarkMode,
              'bg-blue-700 text-gray-100 hover:bg-blue-800': isDarkMode,
              'opacity-50 cursor-not-allowed': isLoading,
            }"
          >
            Next
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import SearchBar from "./SearchBar.vue";
import SearchResultList from "./SearchResultList.vue";
import Loader from "./Loader.vue";
import axios from "axios";

const API_URL = "https://api.unsplash.com/search/photos";
const IMAGES_PER_PAGE = 20;

// Reactive state variables
const searchResults = ref([]);
const isLoading = ref(false);
const searchPerformed = ref(false);
const currentPage = ref(1);
const currentQuery = ref("");
const hasMoreResults = ref(true);
const isDarkMode = ref(false);
const selectedResult = ref(null);
const totalPages = ref(0);

// Function to perform search
const performSearch = async (query) => {
  // Reset search state
  searchResults.value = [];
  isLoading.value = true;
  searchPerformed.value = true;
  currentPage.value = 1;
  currentQuery.value = query;
  hasMoreResults.value = true;

  try {
    await fetchSearchResults(query, currentPage.value);
  } catch (error) {
    console.error("Search error:", error);
    hasMoreResults.value = false;
  } finally {
    isLoading.value = false;
  }
};

const loadMoreResults = async (direction) => {
  if (isLoading.value || !hasMoreResults.value) return;

  isLoading.value = true;
  if (direction === "next") {
    currentPage.value++;
  }
  if (direction === "prev") {
    currentPage.value--;
  }

  try {
    searchResults.value.length = 0;
    await fetchSearchResults(currentQuery.value, currentPage.value);
  } catch (error) {
    console.error("Load more error:", error);
    hasMoreResults.value = false;
  } finally {
    isLoading.value = false;
  }
};

// Function to fetch search results from Unsplash API
const fetchSearchResults = async (query, page = 1) => {
  try {
    const accessKey = import.meta.env.VITE_API_KEY;

    const url = `${API_URL}?query=${query}&page=${page}&per_page=${IMAGES_PER_PAGE}&client_id=${accessKey}`;

    const response = await axios.get(url, {
      headers: {
        Authorization: `Client-ID ${accessKey}`,
      },
    });

    const data = response.data;
    hasMoreResults.value = data.results.length === IMAGES_PER_PAGE;
    totalPages.value = data.total_pages;

    const transformedResults = data.results.map((photo) => ({
      id: photo.id,
      title: capitalizeFirstLetter(photo.alt_description) || "Untitled Image",
      snippet:
        capitalizeFirstLetter(photo.description) || "No description available",
      imageUrl: photo.urls.small,
      details: {
        fullDescription:
          capitalizeFirstLetter(photo.description) ||
          "No detailed description available.",
        metadata: {
          photographer: photo.user.name,
          likes: photo.likes,
          downloadLink: photo.links.download,
          createdAt: new Date(photo.created_at).toLocaleDateString(),
        },
        imageUrl: photo.urls.raw,
      },
    }));

    // Append new results or replace if it's the first page
    searchResults.value = transformedResults;
    // page === 1
    //   ? transformedResults
    //   : [...searchResults.value, ...transformedResults];
  } catch (error) {
    console.error("Failed to fetch results:", error);
    // errorMessage.value = "Failed to fetch search results";
    hasMoreResults.value = false;
  }
};

function capitalizeFirstLetter(string) {
  if (!string) return string;
  return string.charAt(0).toUpperCase() + string.slice(1);
}

const handleResultClick = (result) => {
  selectedResult.value = result;
};

const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value;
  localStorage.setItem("darkMode", isDarkMode.value);
};

onMounted(() => {
  const savedDarkMode = localStorage.getItem("darkMode");
  if (savedDarkMode !== null) {
    isDarkMode.value = JSON.parse(savedDarkMode);
  }
});
</script>
