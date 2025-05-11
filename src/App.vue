<template>
  <div
      :class="[
          'min-h-screen min-w-screen transition-colors duration-1000 ease-in-out flex flex-col gap-10 justify-center items-center py-10 px-4',
          backgroundClass,
      ]"
  >
      <h1>Weather app</h1>
      <div
          class="w-full max-w-4xl bg-white shadow-xl rounded-xl p-6 space-y-8"
      >
          <!-- Form -->
          <div
              class="flex flex-col md:flex-row justify-between items-center gap-4"
          >
              <WeatherForm @search="fetchWeather" />
              <label class="inline-flex items-center cursor-pointer">
                  <input
                      type="checkbox"
                      v-model="useFahrenheit"
                      class="sr-only peer"
                  />
                  <div
                      class="relative w-11 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-blue-300 dark:peer-focus:ring-blue-800 rounded-full peer dark:bg-gray-700 peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:start-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all dark:border-gray-600 peer-checked:bg-blue-600 dark:peer-checked:bg-blue-600"
                  ></div>
                  <span
                      class="ml-3 text-sm font-medium text-gray-900 dark:text-gray-300"
                      >°F</span
                  >
              </label>
          </div>

          <!-- Current weather -->
          <div v-if="weather" class="flex justify-center items-center">
              <div
                  class="grid md:grid-cols-2 gap-10 items-center text-center md:text-left"
              >
                  <!-- Left column -->
                  <div class="space-y-2">
                      <h2 class="text-xl font-semibold">
                          Current weather in
                      </h2>
                      <h3 class="text-2xl font-bold">
                          {{ weather.location.name }}
                      </h3>
                      <p class="text-sm text-gray-500">
                          Time: {{ formatTime(weather.location.localtime) }}
                      </p>
                      <img
                          class="h-20 w-20 mx-auto md:mx-0"
                          :src="weather.current.condition.icon"
                          :alt="weather.current.condition.text"
                      />
                      <p class="text-sm text-gray-700">
                          {{ weather.current.condition.text }}
                      </p>
                  </div>

                  <!-- Right column -->
                  <div class="grid grid-cols-2 gap-x-4 gap-y-2 text-sm">
                      <div class="font-medium text-gray-700">
                          🌡️ Temperature:
                      </div>
                      <div class="text-gray-800">
                          {{
                              useFahrenheit
                                  ? weather.current.temp_f
                                  : weather.current.temp_c
                          }}°{{ useFahrenheit ? "F" : "C" }}
                      </div>

                      <div class="font-medium text-gray-700">
                          🌡️ Feels like:
                      </div>
                      <div class="text-gray-800">
                          {{
                              useFahrenheit
                                  ? weather.current.feelslike_f
                                  : weather.current.feelslike_c
                          }}°{{ useFahrenheit ? "F" : "C" }}
                      </div>

                      <div class="font-medium text-gray-700">
                          💧 Humidity:
                      </div>
                      <div class="text-gray-800">
                          {{ weather.current.humidity }}%
                      </div>

                      <div class="font-medium text-gray-700">💨 Wind:</div>
                      <div class="text-gray-800">
                          {{ weather.current.wind_mph }} mph
                      </div>

                      <div class="font-medium text-gray-700">
                          🌞 UV Index:
                      </div>
                      <div class="text-gray-800">
                          {{ weather.current.uv }}
                      </div>
                  </div>
              </div>
          </div>

          <!-- Forecast -->
          <Forecast
              v-if="forecast"
              :forecast="forecast"
              :useFahrenheit="useFahrenheit"
          />
      </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import WeatherForm from "./components/WeatherForm.vue";
import Forecast from "./components/Forecast.vue";

const useFahrenheit = ref(false);

const weather = ref(null);
const forecast = ref(null);
const error = ref(null);
const loading = ref(false);

async function fetchWeather(cityName) {
  loading.value = true;
  error.value = null;
  weather.value = null;
  forecast.value = null;

  const apikey = import.meta.env.VITE_API_KEY;
  const currentUrl = `https://api.weatherapi.com/v1/current.json?key=${apikey}&q=${cityName}`;
  const forecastUrl = `https://api.weatherapi.com/v1/forecast.json?key=${apikey}&q=${cityName}&days=3`;

  try {
      const [currentRes, forecastRes] = await Promise.all([
          fetch(currentUrl),
          fetch(forecastUrl),
      ]);

      if (!currentRes.ok || !forecastRes.ok) {
          throw new Error("City not found");
      }

      const currentData = await currentRes.json();
      const forecastData = await forecastRes.json();

      weather.value = currentData;
      forecast.value = forecastData;

      setBackground(currentData.location.localtime);
  } catch (err) {
      error.value = err.message || "Something went wrong";
  } finally {
      loading.value = false;
  }
}

const backgroundClass = ref("bg-gradient-to-b from-pink-100 to-pink-200"); // soft daytime default

function setBackground(localTimeString) {
  const hour = Number(localTimeString.split(" ")[1].split(":")[0]);

  if (hour >= 5 && hour < 8) {
      backgroundClass.value = "bg-gradient-to-b from-orange-200 to-pink-300"; // sunrise
  } else if (hour >= 8 && hour < 18) {
      backgroundClass.value = "bg-gradient-to-b from-sky-200 to-sky-300"; // day
  } else if (hour >= 18 && hour < 20) {
      backgroundClass.value = "bg-gradient-to-b from-red-200 to-orange-300"; // sunset
  } else {
      backgroundClass.value = "bg-gradient-to-b from-purple-800 to-gray-900"; // night
  }
}

function formatTime(dateString) {
  const date = new Date(dateString.replace(" ", "T")); // ensure ISO format
  return date.toLocaleTimeString([], {
      hour: "numeric",
      minute: "2-digit",
      hour12: true,
  });
}
</script>

<style scoped></style>
