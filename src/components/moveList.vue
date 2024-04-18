<script setup>
import axios from 'axios'
import { ref } from 'vue'
const props = defineProps({
  movies: Array
})

let infoFilm = ref({})
let isActive = ref(false)

async function getMovieInfo(movie) {
  infoFilm.value = {}
  await axios
    .get(`https://kinopoiskapiunofficial.tech/api/v2.2/films/${movie.filmId || movie.kinopoiskId}`, {
      headers: {
        'X-API-KEY': '240e4627-a28b-4b28-b950-4a3c4010c23e',
        'Content-Type': 'application/json'
      }
    })
    .then((res) => (infoFilm.value = res.data))
}
function checkActive() {
  isActive.value = true
  document.body.style.overflow = 'hidden'
  if (event.target.classList.contains('infoFilm__window')) {
    isActive.value = false
  }
}
</script>

<template>
  <div
    v-for="(movie, index) in props.movies"
    :key="index"
    class="film__card"
    @click="getMovieInfo(movie), checkActive()"
  >
    <img :src="movie.posterUrlPreview" alt="картинка" class="film__card__img" />
    <div
      class="film__reiting__color"
      :class="{
        redbg: Number(movie.rating) <= 1 || movie.ratingKinopoisk <= 1,
        yellowbg: Number(movie.rating) <= 7 || movie.ratingKinopoisk  <= 7,
        greenbg: Number(movie.rating) <= 10 || movie.ratingKinopoisk <= 10
      }"
    >
      <div v-show="movie.ratingKinopoisk || movie.rating != 'null'" class="film__reiting">
        <p>{{ movie.ratingKinopoisk || movie.rating }}</p>
      </div>
    </div>
    <div class="film__name">
      <p>{{ movie.nameOriginal || movie.nameEn }}</p>
    </div>
    <div class="film__genres">
      <p v-for="(gener, index) in movie.genres" :key="index">{{ gener.genre }}</p>
    </div>
  </div>

  <div class="infoFilm__window" v-show="isActive" @click="checkActive">
    <div class="info">
      <div class="img">
        <img :src="infoFilm.posterUrl" alt="картинка" class="film__info__img" />
      </div>
      <div class="film__info__name">
        <p>{{ infoFilm.nameOriginal }}</p>
      </div>
      <div class="film__info__reiting">
        Рейтинг:
        <p
          :class="{
            red: infoFilm.ratingKinopoisk <= 5,
            yellow: infoFilm.ratingKinopoisk <= 7,
            green: infoFilm.ratingKinopoisk <= 10
          }"
        >
          {{ infoFilm.ratingKinopoisk }}
        </p>
      </div>
      <div class="film__info__desc">
        <p><b>Описание:</b> {{ infoFilm.description }}</p>
      </div>
      <div class="film__genres">
        <p v-for="(gener, index) in infoFilm.genres" :key="index">{{ gener.genre }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.film__card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 300px;
  height: 510px;
  position: relative;
  cursor: pointer;
}
.film__card__img {
   height: 450px;
}

.film__reiting {
  width: 100%;
  height: 100%;
  background-color: rgb(61, 61, 61);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.film__reiting__color {
  padding: 2px;
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
  top: 10px;
  right: 10px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  color: #fff;
  font-size: 14px;
}
.film__name {
  color: #fff;
  font-size: 19px;
}
.film__genres {
  display: flex;
  gap: 7px;
  font-size: 10px;
  flex-wrap: wrap;
  color: rgba(0, 0, 0, 0.616);
  font-weight: bold;
}
.film__genres p{
  background-color: rgba(255, 255, 6, 0.582);
  padding: 2px 5px;
  border-radius: 10px;
}
.infoFilm__window {
  position: fixed;
  left: 0;
  bottom: 0;
  right: 0;
  top: 0;
  background-color: rgba(0, 0, 0, 0.452);
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.info {
  background-color: #414141;
  padding: 15px;
  border-radius: 10px;
  overflow: auto;
  width: 650px;
  height: 800px;
  display: flex;
  flex-direction: column;
  row-gap: 15px;
  align-items: start;
  justify-content: space-between;
  text-align: start;
  color: #fff;
  cursor: auto;
}
.img {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}
.film__info__img {
  width: 300px;
  border-radius: 15px;
}
.film__info__reiting {
  display: flex;
  column-gap: 15px;
  font-weight: bold;
  font-size: 25px;
}
.film__info__name {
  font-weight: bold;
  font-size: 23px;
}
.film__info__desc {
  font-size: 13px;
}
.film__info__desc b{
  font-size: 16px;
}
.film__info__genres {
  display: flex;
}
.red {
  color: red;
}
.green {
  color: green;
}
.yellow {
  color: yellow;
}
.redbg {
  background-color: rgb(192, 9, 9);
}
.greenbg {
  background-color: rgba(24, 165, 5, 0.822);
}
.yellowbg {
  background-color: rgb(194, 194, 7);
}
</style>
