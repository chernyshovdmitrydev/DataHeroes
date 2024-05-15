<script setup>
import { reactive, onBeforeMount } from "vue";
import RamCard from "./components/RamCard.vue";

let loading = false;
let charactersData = reactive({});
let episodesData = [];
let filterData = reactive({
  name: "",
  status: "",
});


async function getEpisode(url, page = 1) {
  try {
    loading = true;
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error("респонс не ок");
    }
    let data = await response.json();
    episodesData.push(...data.results);
    if (data.info.next && page < data.info.pages) {
      await getEpisode(data.info.next, page + 1);
    }
  } catch (error) {
    console.error(error);
  }
}

async function getCharacters(url) {
  try {
    loading = true;
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error();
    }
    let data = await response.json();
    data.results.forEach((item) => {
      const episodeId = parseInt(item.episode[0].split("/").pop());
      const array = episodesData.find((ep) => ep.id === episodeId);

      item.firstEpisode = array.name;
    });

    charactersData.value = data;
  } catch (error) {
    charactersData.value = {};
  } finally {
    loading = false;
  }
}

function filter() {
  let url = "https://rickandmortyapi.com/api/character";
  let params = [];
  if (filterData.name) {
    params.push(`name=${filterData.name}`);
  }
  if (filterData.status) {
    params.push(`status=${filterData.status}`);
  }

  if (params.length > 0) {
    url += `?${params.join("&")}`;
  }
  getCharacters(url);
}

onBeforeMount(async () => {
  try {
    await getEpisode("https://rickandmortyapi.com/api/episode");
    await getCharacters("https://rickandmortyapi.com/api/character");
  } catch (error) {
    console.error(error);
  } finally {
    loading = false;
  }
});
</script>

<template>
  <template v-if="charactersData.value">
    <div class="nav-panel">
      <div>
        <button
          @click="
            getCharacters(
              charactersData.value.info ? charactersData.value.info.prev : ''
            )
          "
          :disabled="
            !charactersData.value.info || !charactersData.value.info.prev
          "
        >
          Назад
        </button>
        <button
          @click="
            getCharacters(
              charactersData.value.info ? charactersData.value.info.next : ''
            )
          "
          :disabled="
            !charactersData.value.info || !charactersData.value.info.next
          "
        >
          Вперед
        </button>
      </div>
      <form>
        <input v-model="filterData.name" />
        <select v-model="filterData.status">
          <option value="">Empty</option>
          <option value="alive">Alive</option>
          <option value="dead">Dead</option>
          <option value="unknown">Unknown</option>
        </select>
        <button @click.prevent="filter">search</button>
      </form>
    </div>
  </template>
  <div class="cards">
    <template v-if="charactersData.value?.results?.length > 0">
      <RamCard
        v-for="character in charactersData.value.results"
        :character="character"
        :key="character.id"
      />
    </template>
    <p v-else-if="loading" >loading...</p>
    <p v-else>Not found</p>
  </div>
  
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap");
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: "Roboto", sans-serif;
}
.cards {
  display: flex;
  flex-wrap: wrap;
  max-width: 1920px;
  min-height: 95vh;
  align-items: center;
  justify-content: center;
  gap: 10px;
  background-color: rgb(39, 43, 51);
}

.nav-panel {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  flex-wrap: wrap;
  min-height: 5vh;
  background-color: rgb(39, 43, 51);
}

.nav-panel > * {
  display: flex;
  gap: 3px;
}
p {
  color: white;
  font-size: 40px;
}

.navigation__info {
  color: white;
}
</style>
