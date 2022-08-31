<template>
  <div class="flex flex-col flex-1 items-center">
    <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
      <p>
        you are currently previewing this city, click the "+" icon
        to start tracking this city.
      </p>
    </div>
    <div class="flex flex-col text-white py-12 items-center">
      <h1 class="text-4xl mb-2">{{ route.params.city}}</h1>
      <p class="text-sm mb-12">
        {{ new Date(weatherData.currentTime).toLocaleDateString(
          "en-us",{weekday: "short", day: "2-digit", month: "long"}) }}
        {{ new Date(weatherData.currentTime).toLocaleTimeString(
          "en-us",{timeStyle: "short"}) }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.current.temp) }}&deg;
      </p>
        <p>
          Feels Like
          {{ Math.round(weatherData.current.feels_like) }}&deg;
        </p>
        <p class="capialize">
          {{ weatherData.current.weather[0].description }}
        </p>
        <img class="w-[150px] h-auto" :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" alt="">
    </div>
    <hr class="border-white border-opacity-10 border w-full">
  <!--    hourly weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div v-for="hour in weatherData.hourly" :key="hour.dt" class="flex flex-col gap-4 items-center">
            <p class="whitespace-nowrap text-md">
              {{ new Date(hour.currentTime).toLocaleTimeString("en-us", {hour: "numeric"}) }}
            </p>
            <img class="w-auto h-[50px] object-cover" :src="`http://openweathermap.org/img/wn/${hour.weather[0].icon}@2x.png`" alt="">
            <p class="text-xl">
              {{ Math.round(hour.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full">
<!--    weekly weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">7 Day Forecast</h2>
        <div v-for="day in weatherData.daily" :key="day.dt" class="flex items-center">
          <p class="flex-1">
            {{ new Date(day.dt * 1000).toLocaleDateString("en-us", {weekday: "long"}) }}
          </p>
          <img class="w-[50px] h-[50px] object-cover" :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt="">
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.temp.max)}}</p>
            <p>L: {{ Math.round(day.temp.min)}}</p>
          </div>
        </div>
      </div>
    </div>
    <div @click="removeCity" class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500">
      <i class="fa-solid fa-trash"></i>
      <p>Remove city</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import {useRoute, useRouter} from "vue-router";

const  route = useRoute()
const router = useRouter()

const getWeatherData = async ()=>{
  try {
    const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
    );

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime =
          utc + 1000 * weatherData.data.timezone_offset;
    });
    // flicker delay to show skeleton animated
    await new Promise(res => setTimeout(res, 1000));
    return weatherData.data;
  }catch (err){
    console.log(`Error ${err}`)
  }
}
const weatherData = await getWeatherData()

const removeCity = () =>{
  const city = route.params.city
  const state = route.params.state
  let savedCities = JSON.parse(localStorage.getItem("savedCities"))
  savedCities = savedCities.filter(el =>{
    console.log(el)
    return el.city != city && el.state != state
  })
  localStorage.setItem("savedCities", JSON.stringify(savedCities))
  router.push({name: "home"})
}
</script>
