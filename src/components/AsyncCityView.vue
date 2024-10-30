<template>
    <div class="flex flex-col flex-1 items-center">
        <div v-if="route.query.preview" class="text-white p-4 bg-w-secondary w-full text-center">
            <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
        </div>
        <div class="flex flex-col items-center text-white py-12" v-if="weatherData">
            <h1 class="text-4xl mb-2">{{ weatherData.name }}</h1>
            <p class="text-sm mb-12">
                {{ new Date(weatherData.currentTime).toLocaleDateString("en-us", { weekday: "short", day: "2-digit", month: "long" }) }}
                {{ new Date(weatherData.currentTime).toLocaleTimeString("en-us", { timeStyle: "short" }) }}
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round(weatherData.main.temp - 273.15) }}&deg;C
            </p>
            <div class="text-center">
                <p>Feels like {{ Math.round(weatherData.main.feels_like - 273.15) }}&deg;C</p>
                <p class="capitalize">{{ weatherData.weather[0].description }}</p>
                <img class="w-[150px] h-auto" :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="weather icon">
            </div>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const weatherData = ref(null);

const getWeatherData = async () => {
    try {
        const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=53e22a5cc1be701762afe165bf5f3998`
        );

        // Extract and format current time
        const currentTime = new Date().getTime() + response.data.timezone * 1000;

        // Add currentTime to the response data
        response.data.currentTime = currentTime;

        // Assign response to weatherData for display in the template
        weatherData.value = response.data;
    } catch (error) {
        console.error("Error fetching weather data:", error);
    }
};

getWeatherData();
</script>