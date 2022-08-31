<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
          type="text"
          v-model="searchQuery"
          @input="getSearchResult"
          placeholder="Search for a city or state"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-primary focus:outline-none focus:shadow-[0px_1px_0_0_#004e71" />
      <ul v-if="mapSearchResult" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 to-[66px]">
        <p v-if="searchErr">
            Error something went wrong, please try again
        </p>
        <p v-if="!searchErr && mapSearchResult.length === 0">
          no search result match your query, try a different term
        </p>
        <template v-else>
          <li @click="previewCity(city)" class="cursor-pointer px-1 hover:bg-weather-primary" v-for="city in mapSearchResult" :key="city.id">
              {{ city.place_name}}
          </li>
        </template>
      </ul>
    </div>
   <div class="flex flex-col gap-4">
     <Suspense>
       <CityList />
       <template #fallback>
        <CityCardSkeleton />
       </template>
     </Suspense>
   </div>
  </main>
</template>

<script setup>
import {ref} from "vue";
import axios from 'axios'
import {useRouter} from 'vue-router'
import CityList from "@/components/CityList";
import CityCardSkeleton from "@/components/CityCardSkeleton";

const router = useRouter()
const previewCity = serchResult =>{
  console.log(serchResult)
  const [city, state] = serchResult.place_name.split(",")
  router.push({
    name: 'cityView',
    params: {state: state.replaceAll(' ', ''), city},
    query:{
      lat: serchResult.geometry.coordinates[1],
      lng: serchResult.geometry.coordinates[0],
      preview: true
    }
  })
}

const mapboxAPIKEY = "pk.eyJ1IjoidGFoaXJ6YXFvdXQiLCJhIjoiY2w3YnEwNnl0MTRvZTNxbzhwNjlpcmtjMSJ9.VU98oCIeXU5ElJdactNE1A"
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapSearchResult = ref(null)
const searchErr = ref(null)

const getSearchResult = ()=>{
  clearTimeout(queryTimeout)
  queryTimeout.value = setTimeout(async ()=>{
    if(searchQuery.value != ""){
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKEY}&type=place`)
        mapSearchResult.value = result.data.features;
        console.log(mapSearchResult.value)
      }
      catch (e){
        searchErr.value = e.message
      }
      return;
    }
    mapSearchResult.value = null
  }, 300)
}
</script>