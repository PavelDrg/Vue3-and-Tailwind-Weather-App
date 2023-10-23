<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="searchResults"
      >
        <li
          v-for="result in searchResults.data"
          :key="result.id"
          class="py-2 cursor-pointer"
        >
          {{ result.display_name }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const searchQuery = ref("");
const queryTimeout = ref(null);
const searchResults = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      const result = await axios.get(
        `https://geocode.maps.co/search?q=${searchQuery.value}`
      );
      searchResults.value = result;
      return;
    }
    searchResults.value = null;
  }, 300);
};
</script>

<!-- searchResults.value.data[0].display_name -->
