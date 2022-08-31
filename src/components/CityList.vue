<template>
  <div v-for="city in cities" :key="city.id">
    <CityCard :city="city" @click="viewCity(city)"/>
  </div>
  <p v-if="cities.length === 0">
    no location added. to start tracking a location, search in the field above
  </p>
</template>

<script setup>

import {ref} from "vue";
import axios from "axios";
import CityCard from "@/components/CityCard";
import router from "@/router";

const cities = ref([])
const getCities = async ()=>{
  if(localStorage.getItem("savedCities")){
    cities.value = JSON.parse(localStorage.getItem("savedCities"))
  }
  const requests = []
  cities.value.forEach(city => {
    requests.push(
    axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`))
  })
  const weatherData = Promise.all(requests);
  // flicker delay to show skeleton
  await new Promise(res => setTimeout(res, 1000));
  (await weatherData).forEach((val, idx)=>{
    cities.value[idx].weather = val.data
  })
}
await getCities()

const viewCity = city =>{
  router.push({
    name: 'cityView',
    params: {state: city.state, city: city.city},
    query: {lat: city.coords.lat, lng: city.coords.lng}
  })
}
</script>


