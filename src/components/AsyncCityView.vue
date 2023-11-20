<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>
    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{ weatherData.currentTime }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData?.data?.main?.temp) }}&deg;
      </p>

      <p>
        Feels like {{ Math.round(weatherData?.data.main?.feels_like) }}&deg;
      </p>
      <p class="capitalize">
        {{ weatherData?.data?.weather[0]?.description }}
      </p>
      <img
        class="w-[150px] h-auto"
        :src="`https://openweathermap.org/img/wn/${weatherData.data.weather[0].icon}@2x.png`"
        alt="icon"
      />
    </div>

    <hr class="border-white border-opacity-10 border w-5/6" />

    <div
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
import { format } from "date-fns";

const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lon}&exclude=current,minutely,daily&appid=ebedbbc7942a9377d8ca0a50bf75ae4c&units=metric&hourly=true`
    );

    // calc current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.dt * 1000 + localOffset;
    weatherData.currentTime = format(
      new Date(utc + 1000 * weatherData.data.timezone),
      "eee, dd MMM, h:mm a"
    );

    // console.log(weatherData);

    //Flicker Delay
    await new Promise((res) => setTimeout(res, 1000));

    return weatherData;
  } catch (err) {
    console.log(err);
  }
};
const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({
    name: "home",
  });
};
</script>
