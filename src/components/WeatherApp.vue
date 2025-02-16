<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const data = ref(null);
const city = ref("");
const darkMode = ref(false);

const defaultCity = "New York";  

onMounted(() => {
  getWeatherData(defaultCity);  
});

async function getWeatherData(cityName) {
  try {
    const apiKey = import.meta.env.VITE_API_KEY;    
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=${apiKey}&units=metric`;
    const resp = await axios.get(url);
    if (resp.data) {
      data.value = resp.data;
    }
  } catch (error) {
    data.value = null;
    console.error("Error fetching weather data:", error);
  }
}

function handleInputChange(event) {
  city.value = event.target.value;
  if (city.value.trim() !== "") {
    getWeatherData(city.value);
  } else {
    data.value = null; 
  }
}

function toggleDarkMode() {
  darkMode.value = !darkMode.value;
}

</script>

<template>
    <div
      :class="[
        'flex justify-center items-center min-h-screen px-4 sm:px-6 transition-all relative',
        darkMode
          ? 'bg-gradient-to-br from-gray-900 via-black to-gray-800 text-white'
          : 'bg-gradient-to-br from-indigo-400 via-blue-300 to-purple-400 text-black',
      ]"
    >
      <div class="fixed top-6 right-6">
        <button
          @click="toggleDarkMode"
          class="px-4 py-2 rounded-lg font-semibold transition-all cursor-pointer w-full sm:w-auto"
          :class="darkMode ? 'bg-gray-700 text-white' : 'bg-yellow-400 text-gray-900'"
        >
          {{ darkMode ? "Light Mode" : "Dark Mode" }}
        </button>
      </div>
  
      <div
        :class="[
          'w-full max-w-lg shadow-lg rounded-2xl p-6 sm:p-8 text-center border transition-all',
          darkMode
            ? 'bg-white/10  border-white/20 '
            : 'bg-white border-gray-300',
        ]"
      >
        <div class="flex flex-col items-center gap-2 p-2">
          <h1 class="text-2xl sm:text-3xl font-bold">Weather App</h1>
          <p class="text-gray-400 text-sm sm:text-base">Get real-time weather updates for any city.</p>
        </div>
  
        <div class="mb-4 w-full">
          <input
            type="text"
            :value="city"
            @input="handleInputChange"
            placeholder="Enter City Name..."
            class="w-full p-3 rounded-lg text-lg focus:outline-none transition-all  sm:text-left"
            :class="darkMode ? 'bg-gray-800 text-white border-gray-600' : 'bg-gray-100 text-black border-gray-300'"
          />
        </div>
  
        <div v-if="data">
          <h1 class="text-3xl sm:text-4xl font-bold drop-shadow-lg">{{ data.name }}</h1>
          <p class="text-5xl sm:text-6xl font-extrabold mt-2 drop-shadow-lg">
            {{ data.main.temp }}°C
          </p>
          <p class="text-lg sm:text-xl capitalize mt-2">
            {{ data.weather[0].description }}
          </p>
  
          <div class="flex justify-center mt-2">
            <img
              v-if="data.weather[0].icon"
              :src="`https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`"
              alt="Weather Icon"
              class="w-20 h-20 sm:w-28 sm:h-28"
            />
          </div>
  
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-6 mt-4">
            <div
              class="p-4 sm:p-5 rounded-lg shadow-md text-center transition-all"
              :class="darkMode ? 'bg-gray-800 border border-gray-600' : 'bg-gray-100 border border-gray-300'"
            >
              <p class="text-sm">Wind Speed</p>
              <p class="text-2xl font-semibold">{{ data.wind.speed }} m/s</p>
            </div>
            <div
              class="p-4 sm:p-5 rounded-lg shadow-md text-center transition-all"
              :class="darkMode ? 'bg-gray-800 border border-gray-600' : 'bg-gray-100 border border-gray-300'"
            >
              <p class="text-sm">Humidity</p>
              <p class="text-2xl font-semibold">{{ data.main.humidity }}%</p>
            </div>
          </div>
        </div>
  
        <div v-else class="flex flex-col items-center gap-4 p-6">
          <img src="https://cdn-icons-png.flaticon.com/512/1146/1146869.png" alt="No Data" class="w-20 sm:w-24 h-20 sm:h-24 opacity-50" />
          <p class="text-lg sm:text-xl font-semibold text-gray-400">Enter a valid city name to get weather updates</p>
        </div>
      </div>
    </div>
  </template>
  