<template>
  <div>
    <header>
      <h1>Boolflix</h1>
      <div class="searchbar">
        <input type="text" v-model="cerca" />
        <button @click="search(cerca)">Cerca</button>
      </div>
    </header>

    <main>
      <h1>Per te:</h1>
      <!-- Piccola licenza 'blasfema per la spaziatura dell'emoji -->
      <h2 style="font-size: 40px">&#127909; &nbsp;Film</h2>
      <section>
        <div class="card" v-for="movie in movies" :key="movie.id">
          <img
            class="posterimg"
            :src="`https://image.tmdb.org/t/p/w342/${movie.poster_path}`"
            alt=""
          />
          <ul class="infor">
            <li><strong>Title:</strong> {{ movie.title }}</li>
            <li><strong>Original Title:</strong> {{ movie.original_title }}</li>

            <li>
              <img
                class="flag"
                v-if="hasFlag(movie)"
                :src="srcFlag(movie)"
                alt=""
              />

              <span v-else>{{ movie.original_language }}</span>
            </li>
            <li><strong>Vote:</strong> {{ movie.vote_average }}</li>
          </ul>
        </div>
      </section>

      <h2 style="font-size: 40px">
        &#128421; &nbsp;Broadcast (Serie tv, web series, ecc...)
      </h2>
      <section>
        <div class="card" v-for="serie in series" :key="serie.id">
          <img
            class="posterimg"
            :src="`https://image.tmdb.org/t/p/w342/${serie.poster_path}`"
            alt=""
          />
          <ul>
            <li><strong>Title:</strong> {{ serie.name }}</li>
            <!-- <li><img :src= "`https://image.tmdb.org/t/p/w92 ${movie.poster_path}`" alt=""></li>   -->
            <li><strong>Original Title:</strong> {{ serie.original_name }}</li>
            <li>
              <img
                class="flag"
                v-if="hasFlag(serie)"
                :src="srcFlag(serie)"
                alt=""
              />

              <span v-else>{{ serie.original_language }}</span>
            </li>            
            <li><strong>Vote:</strong> {{ serie.vote_average }}</li>
          </ul>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Header",

  data() {
    return {
      countries: ["it", "en"],
      movies: [],
      series: [],
      cerca: "",
      api: {
        language: "it-IT",
        baseUri: "https://api.themoviedb.org/3",
        key: "101a61aa934c613d880b033e114051e0",
      },
    };
  },

  methods: {
    srcFlag(item) {
      return require(`@/assets/img/${item.original_language}.png`);
    },
    hasFlag(item) {
      return this.countries.includes(item.original_language);
    },

    search(cerca) {
      if (!cerca) {
        this.movies = [];
        this.series = [];
        return;
      }
      const { key, language } = this.api;
      const config = {
        params: {
          language,
          api_key: key,
          query: cerca,
        },
      };

      this.fetchMovieseries("search/movie", config, "movies");
      this.fetchMovieseries("search/tv", config, "series");
    },

    fetchMovieseries(endpoint, config, target) {
      axios
        .get(`${this.api.baseUri}/${endpoint}`, config)
        .then((res) => {
          if (target == "movies") {
            this.movies = res.data.results;
          } else {
            this.series = res.data.results;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style>
* {
  font-family: "Raleway", sans-serif;
  font-family: "Roboto", sans-serif;
  padding: 0;
  margin: 0;
}

header {
  padding: 20px;
  height: 35px;
  background-color: #e16f00;
  display: flex;
  justify-content: space-between;
}

h1 {
  margin-bottom: 40px;
}

body {
  background-color: dodgerblue;
  color: white;
}

section {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin: 0;
  margin-bottom: 20px;
}

main {
  padding: 20px;
}

.card {
  /* height: 160px;
  width: calc((100vw / 12) * 2); */
  background-color: #e16f00;
  margin: 10px;
  padding: 0;
  width: 342px;
  height: 513px;
  border-radius: 20px;
}

ul {
  max-width: 650px;
  list-style: none;
  position: relative;
  margin: 20px;
}

/* Stronger and cooler */
strong {
  font-size: larger;
  color: dodgerblue;
}

li {
  color: white;
}

.posterimg {
  position: absolute;
  z-index: 5;
  height: 513px;
  width: 342px;
  border-radius: 20px;
}

.posterimg:hover {
  display: none;
}

.flag {
  width: 50px;
  height: 40px;
}

h2 {
  margin-bottom: 40px;
}
</style>

