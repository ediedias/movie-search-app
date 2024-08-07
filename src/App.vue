<script setup>
import { ref } from 'vue';
import axios from 'axios';

const apiKey = 'YOUR_API_KEY';
const query = ref('');
const movies = ref([]);
const loading = ref(false);
const error = ref('');

const searchMovies = async () => {
  if(!query.value) {
    error.value = 'Please enter a movie name';
    return;
  }

  loading.value = true;
  
  try {
    const response = await axios.get(`http://www.omdbapi.com/?apikey=${apiKey}&s=${query.value}`);
    if (response.data.Response === 'True') {
      movies.value = response.data.Search;      
    } else {
      error.value = response.data.Error;
      movies.value = [];
    }
  } catch(err) {
    error.value = 'An error occurred while fetching the data';
  } finally {
    loading.value = false;
  }

    
}

</script>

<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xk font-bold mb-4">Movie Search App</h1>
    <div class="mb-4">
      <input 
        v-model="query" 
        @keyup.enter="searchMovies" type="text" class="border p-2 w-full" placeholder="Search for a movie">
    </div>
    <button @click="searchMovies" class="bg-blue-500 text-white px-4 py-2 rounded">Search</button>
    <div v-if="loading" class="mt-4">Loading...</div>
    <div v-if="error" class="mt-4 text-red-500">{{ error }}</div>
    <div v-if="movies.length" class="flex flex-wrap gap-4 mt-4">
      <div v-for="movie in movies" :key="movie.imdbID" class="border p-2 mb-2 w-64">
        <h2 class="text-xl text-wrap">{{  movie.Title }}</h2>
        <p>{{ movie.Year }}</p>
        <img :src="movie.Poster" class="w-64 h-72 object-cover" alt="Poster"/>
      </div>
    </div>
  </div>
</template>