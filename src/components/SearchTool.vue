<template>
  <div
    class="min-h-screen transition-colors duration-300"
    :class="{
      'bg-gray-100 text-gray-900': !isDarkMode,
      'bg-gray-900 text-gray-100': isDarkMode,
    }"
  >
    <div class="max-w-4xl mx-auto px-4 py-8">
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

// Reactive state variables
const searchResults = ref([]);
const isLoading = ref(false);
const searchPerformed = ref(false);
const currentPage = ref(1);
const currentQuery = ref("");
const hasMoreResults = ref(true);
const isDarkMode = ref(false);
const selectedResult = ref(null);

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

const loadMoreResults = async () => {
  if (isLoading.value || !hasMoreResults.value) return;

  isLoading.value = true;
  currentPage.value++;

  try {
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
  const accessKey = process.env.VITE_API_KEY;
  console.log(accessKey);
  const url = `https://api.unsplash.com/search/photos?query=${query}&page=${page}&per_page=10`;

  const response = await fetch(url, {
    headers: {
      Authorization: `Client-ID ${accessKey}`,
    },
  });

  if (!response.ok) {
    throw new Error("Failed to fetch results");
  }

  const data = await response.json();

  // If no results or less than 10, we've reached the end
  hasMoreResults.value = data.results.length === 10;

  // Transform Unsplash results to our format
  const transformedResults = data.results.map((photo) => ({
    id: photo.id,
    title: photo.alt_description || "Untitled Image",
    snippet: photo.description || "No description available",
    imageUrl: photo.urls.small,
    details: {
      fullDescription:
        photo.description || "No detailed description available.",
      metadata: {
        photographer: photo.user.name,
        likes: photo.likes,
        downloadLink: photo.links.download,
        createdAt: new Date(photo.created_at).toLocaleDateString(),
      },
    },
  }));

  // Append new results or replace if it's the first page
  searchResults.value =
    page === 1
      ? transformedResults
      : [...searchResults.value, ...transformedResults];
};

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
