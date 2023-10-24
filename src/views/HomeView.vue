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
        <p class="py-2" v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p
          class="py-2"
          v-if="!serverError && Object.keys(searchResults.data).length === 0"
        >
          No locations found, try a different term.
        </p>
        <template v-else>
          <li
            v-for="result in searchResults.data"
            :key="result.id"
            class="py-2 cursor-pointer"
            @click="previewCity(result)"
          >
            {{ result.display_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const router = useRouter();
const previewCity = (result) => {
  console.log(result);
  const [city, ...rest] = result.display_name.split(",");
  const [country] = rest.reverse();
  router.push({
    name: "cityView",
    params: {
      country: country.replaceAll(" ", ""),
      city: city.replaceAll(" ", "-"),
    },
    query: {
      lat: result.lat,
      lon: result.lon,
      preview: true,
    },
  });
};

const searchQuery = ref("");
const queryTimeout = ref(null);
const searchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://geocode.maps.co/search?q=${searchQuery.value}`
        );
        searchResults.value = result;
      } catch {
        searchError.value = true;
      }

      return;
    }
    searchResults.value = null;
  }, 300);
};
</script>

<!-- searchResults.value.data[0].display_name -->
