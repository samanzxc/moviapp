<script setup>
import { ref, onMounted, watch } from 'vue'
import axios from 'axios'
import moveList from './moveList.vue'

const API_KEY = '240e4627-a28b-4b28-b950-4a3c4010c23e'
let page = ref(1)
let movies = ref([])
let serch = ref()

// ПОЛУЧАЕМ ФИЛЬМЫ
async function getMove() {
  let API = ref(
    `https://kinopoiskapiunofficial.tech/api/v2.2/films/collections?type=TOP_250_MOVIES&page=${page.value}`
  )
  movies.value = []
  await axios
    .get(API.value, {
      headers: {
        'X-API-KEY': API_KEY,
        'Content-Type': 'application/json'
      }
    })
    .then((res) =>
      res.data.items.forEach((el) => {
        movies.value.push(el)
      })
    )
}
watch(page, getMove)
onMounted(getMove)
// ПОЛУЧАЕМ ФИЛЬМЫ

// ПОИСК ПО ФИЛЬМАМ
async function serchMovie() {
  movies.value = []
  if (serch.value) {
    try {
      await axios
        .get(
          `https://kinopoiskapiunofficial.tech/api/v2.1/films/search-by-keyword?keyword=${serch.value}&page=1`,
          {
            headers: {
              'X-API-KEY': API_KEY,
              'Content-Type': 'application/json'
            }
          }
        )
        .then((res) => {
          res.data.films.forEach((el) => movies.value.push(el))
        })
    } catch (er) {
      console.log('ошибка: ' + er)
    }
  }
}
// ПОИСК ПО ФИЛЬМАМ

// НА ГЛАВНУЮ
async function goHome() {
  movies.value = []
  page.value = 1
  getMove()
}
// НА ГЛАВНУЮ
</script>

<template>
  <main>
  <header>
    <div class="header__logo" @click="goHome">MOVIE APP</div>
    <div class="serch">
      <input
        type="serch"
        v-model="serch"
        @keyup.enter="serchMovie"
        class="header__serch"
        placeholder="Найти"
      />
      <div @click="serchMovie" class="serch__btn"><img src="/src/assets/photo/search.png" alt="search" /></div>
    </div>
  </header>
  <div class="movie__container">
    <moveList :movies="movies" />
  </div>
</main>
  <v-pagination class="pagination" v-model="page" :length="5"></v-pagination>
</template>

<style scoped>
header {
  display: flex;
  justify-content: space-between;
  padding: 30px 120px;
}
.header__logo {
  font-size: 25px;
  color: #fff;
  cursor: pointer;
}
.header__serch {
  background-color: #fff;
  padding: 6px 17px;
  border-radius: 10px 0px 0px 10px;
}
.serch {
  display: flex;
}
.serch__btn {
  height: 100%;
  background-color: #fff;
  display: flex;
  align-items: center;
  border-radius: 0 10px 10px 0;
  padding: 0 10px;
  position: relative;
  cursor: pointer;
}
.serch__btn::before{
  content: '';
  position: absolute;
  width: 1px;
  height: 80%;
  background-color: rgba(0, 0, 0, 0.178);
  left: 0;
}
.serch__btn:hover{
  background-color: #dbdbdb;
}
.serch__btn img{
  width: 30px;
  opacity: .7;
}
.movie__container {
  padding: 60px 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 40px;
}
.pagination {
  color: #fff;
  margin-bottom: 25px;
}
</style>
