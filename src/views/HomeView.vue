<template>
    <div class="hero-section">
      <div class="mainMoivePoster" :style="{ backgroundImage: `url(${mainMovieBackdrop})` }"></div>
      <div class="container">
        <header>
          <div class="headerContent">
            <h3 class="logo">Imovies</h3>
            <input
              type="text"
              placeholder="Search Movies & Seris"
              class="searchBox"
              v-model="searchQuery"
              @input="handleSearchInput"
              ref="searchBox" />
  
            <div
              class="searchResult"
              v-show="showSearchResults && moviesData.length > 0"
              @click="handleSearchResultClick">
              <ul>
                <li class="results" v-for="movie in moviesData" :key="movie.id" @click.prevent>
                  <div
                    class="moviePoster"
                    :style="{ backgroundImage: `url(${getImageUrl(movie.backdrop_path)})` }"
                    @click="displayMovieInfo(movie.original_title, movie.overview)"
                  ></div>
                  <p class="movieName">{{ movie.original_title }}</p>
                </li>
              </ul>
            </div>
          </div>
        </header>
        <div class="movieDesc">
          <h2 class="posterMovieName">{{ displayedMovieName }}</h2>
          <p class="posterrMovieOverview">{{ displayedMovieOverview }}</p>
          <div class="buttons">
            <button class="watchNow">Watch Now</button>
            <button class="addToList">Add To Watch List</button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import axios from 'axios';
  import { ref, onMounted, watch } from 'vue';
  
  const apiKey = 'd87c3e2a2c5209d57f79d67ece881aa8';
  const baseUrl = 'https://api.themoviedb.org/3';
  
  const moviesData = ref([]);
  const showSearchResults = ref(false);
  const searchQuery = ref('');
  const mainMovieBackdrop = ref('');
  const displayedMovieName = ref('');
  const displayedMovieOverview = ref('');

  async function fetchApiData() {
    try {
      const response = await axios.get(`${baseUrl}/discover/movie`, {
        params: {
          api_key: apiKey,
        }
      });
  
      moviesData.value = response.data.results;
      setMainMovieBackdrop();
    } catch (error) {
      console.error('Error during fetching Data', error);
    }
  }
  
  async function fetchSearchResults() {
    try {
      if (searchQuery.value !== '') {
        const response = await axios.get(`${baseUrl}/search/movie`, {
          params: {
            api_key: apiKey,
            query: searchQuery.value
          }
        });
  
        moviesData.value = response.data.results;
      } else {
        fetchApiData();
      }
    } catch (error) {
      console.error('Error during fetching Search Results', error);
    }
  }
  
  function getImageUrl(path) {
    return `https://image.tmdb.org/t/p/w1280${path}`;
  }
  
  function setMainMovieBackdrop() {
    if (moviesData.value.length > 0) {
      const randomIndex = Math.floor(Math.random() * moviesData.value.length);
      mainMovieBackdrop.value = getImageUrl(moviesData.value[randomIndex].backdrop_path);
      displayedMovieName.value = moviesData.value[randomIndex].original_title;
      displayedMovieOverview.value = moviesData.value[randomIndex].overview;
    }
  }
  
  function handleSearchInput() {
    showSearchResults.value = searchQuery.value !== '' && moviesData.value.length > 0;
  }
  
  function handleSearchResultClick(event) {
    if (!event.target.closest('.searchResult') && !event.target.closest('.searchBox')) {
      showSearchResults.value = false;
    }
  }
  
  function displayMovieInfo(name, overview) {
    displayedMovieName.value = name;
    displayedMovieOverview.value = overview;
  }
  
  onMounted(() => {
    fetchApiData();
  });
  
  watch(searchQuery, () => {
    fetchSearchResults();
  });
  
  watch(showSearchResults, newValue => {
    if (newValue) {
      document.addEventListener('click', handleSearchResultClick);
    } else {
      document.removeEventListener('click', handleSearchResultClick);
    }
  });
  </script>
  

  

  

<style scoped>

.hero-section{
    height: 100vh;
    width: 100%;
    background-color: rgb(21, 21, 21);
}

.mainMoivePoster{
    width: 100%;
    height: 100%;
    position: absolute;
    background-size: cover;
    background-repeat: no-repeat;
    background-position:center;
    opacity: 0.6;
}

.container{
    height: 100%;
    width: 100%;
}

header{
    height: 4rem;
    width: 100%;
    display: flex;
    justify-content: center;
    padding-top: 1rem;
    position: relative;
}

.headerContent{
    width: 90%;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.searchBox{
    height: 3.8rem;
    width: 29rem;
    padding-left: 1rem;
    font-size: 1.6rem;
    border-radius: 0.8rem;
    border: none;
    outline: none;
    color: #292828;
}

.logo{
    font-size:2rem;
    letter-spacing: 0.09rem;
}

.searchResult {
    position: absolute;
    right: 7.5rem;
    top: 5.4rem;
    height: 40rem;
    width: 30rem;
    background-color: white;
    border-radius: 1rem;
    overflow-y: auto;
}




ul{
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
}

.results{
    height: 8rem;
    width: 100%;
    display: flex;
    align-items: center;
    gap: 2rem;
}


.results:hover{
    background-color: #dbdbdb;
    cursor: pointer;

}


.moviePoster{
    height: 100%;
    width: 15rem;
    background-size: cover;
    background-position: center;
}

.movieName{
    font-size:1.2rem;
    color: black;
    width: 12rem;
    padding-right: 0.9rem;
}


.movieDesc{
    position: absolute;
    top: 20rem;
    left: 7rem;
    height: 40rem;
    width: 60rem;
    max-width: 90rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}



.posterMovieName{
    font-size: 6rem;
}

.posterrMovieOverview{
    font-size: 1.6rem;
    opacity: 0.8;
}


.buttons{
    display: flex;
    gap: 4rem;
}



.watchNow{
    padding: 2rem 4rem;
    background-color: red;
    border: none;
    border-radius: 50rem;
    font-size: 1.4rem;
    transition: all 0.1s ease-in-out;
}

.watchNow:hover{
    transform: scale(1.01);
    cursor: pointer;
}

.addToList{
    background: none;
    border: none;
    font-size: 1.6rem;
}


.addToList:hover{
    cursor: pointer;
    opacity: 0.7;
}






</style>