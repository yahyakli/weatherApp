<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-w-secondary w-full text-center">
            <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
        </div>
        
        <!-- Current Weather Section -->
        <div class="flex flex-col items-center text-white py-12" v-if="weatherData">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.dt * 1000).toLocaleDateString("en-us", {
                        weekday: "short",
                        day: "2-digit",
                        month: "long",
                    })
                }}
                {{
                    new Date(weatherData.dt * 1000).toLocaleTimeString("en-us", {
                        timeStyle: "short",
                    })
                }}
            </p>
            <p class="text-8xl mb-8">{{ Math.round(weatherData.main.temp - 273.15) }}&deg;C</p>
            <div class="text-center">
                <p>Feels like {{ Math.round(weatherData.main.feels_like - 273.15) }}&deg;C</p>
                <p class="capitalize">{{ weatherData.weather[0].description }}</p>
                <img
                    class="w-[150px] h-auto"
                    :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
                    alt="weather icon"
                >
            </div>
        </div>

        <!-- Wind, Humidity, and Pressure -->
        <div v-if="weatherData" class="text-white">
            <div class="flex gap-4 text-center py-4">
                <p>Wind: {{ weatherData.wind.speed }} m/s</p>
                <p>Humidity: {{ weatherData.main.humidity }}%</p>
                <p>Pressure: {{ weatherData.main.pressure }} hPa</p>
            </div>
        </div>

        <!-- Remove City Button -->
        <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
            <i class="fa-solid fa-trash"></i>
            <p>Remove City</p>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const router = useRouter();
const weatherData = ref(null);

const getWeatherData = async () => {
    try {
        const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=53e22a5cc1be701762afe165bf5f3998`
        );

        // Assign the response data to weatherData
        weatherData.value = response.data;
    } catch (err) {
        console.error("Error fetching weather data:", err);
    }
};

const cities = JSON.parse(localStorage.getItem('savedCities')) || [];
const removeCity = () => {
    const updatedCities = cities.filter(city => city.id !== route.query.id);
    localStorage.setItem('savedCities', JSON.stringify(updatedCities));
    router.push({ name: 'home' });
};

// Fetch weather data on component mount
onMounted(getWeatherData);
</script>
