
<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input @input="getSearchResault" v-model="searchQuery" type="text" placeholder="Search for a city or state" class="py-2 px-1 w-full bg-transparent border-b focus:border-w-secondary focus:outline-0 focus:shadow-[0px_1px_0_0_#004E71]">
      <ul v-if="mapSearchResault" class="absolute bg-w-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <p v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p v-if="!searchError && mapSearchResault.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="searchResault in mapSearchResault" 
            :key="searchResault.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResault)"
          >
            {{ searchResault.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';


const router = useRouter();
const mapboxAPIKey ="pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapSearchResault = ref(null);
const searchError = ref(null);

const previewCity = (searchResault) => {
  const [city, state] = searchResault.place_name.split(",")
  router.push({
    name: "cityView",
    params: {state: state.replaceAll(" ", ""), city: city},
    query: {
      lat:searchResault.geometry.coordinates[1],
      lng: searchResault.geometry.coordinates[0],
      preview: true,
    }
  });
}


const getSearchResault = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout( async () => {
    if(searchQuery.value !== ""){
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapSearchResault.value = result.data.features;
      }catch{
        searchError.value = true;
      }
      return;
    }
    mapSearchResault.value = null;
  }, 300)
}
</script>