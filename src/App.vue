<template>
  <!-- inicio pg home -->
  <div v-if="page == 'home'">
    <div>
      <div class="d-flex justify-content-center p-4">
        <input v-model="index_pokemons" class="bs-tertiary-color rounded-2" type="number" placeholder="Informe um número">
        <button @click="getPokemons" class="btn btn-info">Pesquisar</button>
      </div>
    </div>
    <div style="padding-bottom: 3rem;">
      <div v-for="pokemon in all_pokemons" class="p-3">
        <div class="card mb-3">
          <div class="row g-0">
            <div class="col-md-8">
              <div class="card-body" style="background-color: rgba(243, 243, 243, 0.86);">
                <div class="d-flex">
                  <div class="d-flex justify-content-center"
                    style="width: 30%; background-color: rgba(238, 238, 238, 0.86);">
                    <img height="80"
                      :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${pokemon.id}.png`">
                  </div>
                  <div style="width: 70%; padding-left: 1.5rem;">
                    <h1> {{ pokemon.name }} </h1>
                    <button @click="Details(pokemon.id)" type="button" class="btn btn-transparent btn-sm">
                      <span>Mais informações</span>
                      <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-text-plus" width="24"
                        height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none"
                        stroke-linecap="round" stroke-linejoin="round">
                        <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
                        <path d="M19 10h-14"></path>
                        <path d="M5 6h14"></path>
                        <path d="M14 14h-9"></path>
                        <path d="M5 18h6"></path>
                        <path d="M18 15v6"></path>
                        <path d="M15 18h6"></path>
                      </svg>
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- inicio da pg de um unico pokemon -->
  <div v-if="page == 'information'">
    <div class="d-flex justify-content-center p-4">
      <input v-model="pokemon_numero" class="bs-tertiary-color rounded-2" type="text" placeholder="Informe um pokémon">
      <button class="btn btn-info" @click="searchPokemon">Pesquisar</button>
    </div>
    <div v-if="store_pokemon.id" class="col d-flex flex-column align-items-center justify-content-center" style="padding-top: 8rem;">
      <span style="color: aliceblue;"> ID: {{ store_pokemon.id }}, {{ store_pokemon.name }}</span>
      <img height="350"
        :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${store_pokemon.id}.png`">
      <!-- <h1>{{ store_pokemon.name }}</h1> -->
    </div>

    <!-- <div v-if="store_barrie.name" class="col d-flex flex-column align-items-center justify-content-center">
      <h1>Barrie: {{ store_barrie.name }}</h1>
      <h1>Power: {{ store_barrie.natural_gift_power }}</h1>
      <h1>Size: {{ store_barrie.size }}</h1>
    </div> -->

  </div>
  <div>
    <div class="bg-dark fixed-bottom p-3" style="width: 100%; height: 65px;">
      <div class="d-flex justify-content-around">
        <button @click="page = 'home'" class="btn btn-info">
          <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-home" width="24" height="24"
            viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round"
            stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
            <path d="M5 12l-2 0l9 -9l9 9l-2 0"></path>
            <path d="M5 12v7a2 2 0 0 0 2 2h10a2 2 0 0 0 2 -2v-7"></path>
            <path d="M9 21v-6a2 2 0 0 1 2 -2h2a2 2 0 0 1 2 2v6"></path>
          </svg>
        </button>
        <button @click="page = 'information'" class="btn btn-info">
          <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-search" width="24" height="24"
            viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round"
            stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
            <path d="M10 10m-7 0a7 7 0 1 0 14 0a7 7 0 1 0 -14 0"></path>
            <path d="M21 21l-6 -6"></path>
          </svg>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue';


const page = ref("home")
const pokemon_numero = ref('')
const index_pokemons = ref(null)
const all_pokemons = ref([])


const store_pokemon = ref({
  id: 0,
  name: ''
})

// const barrie_number = ref(1)

// const store_barrie = ref({
//   name: '',
//   natural_gift_power: 0,
//   size: 0
// })

const searchPokemon = async () => {
  const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemon_numero.value}`)

    store_pokemon.value = {
      id: response.data.id,
      name: response.data.name,
    }
    // await searchBarrie()
  console.log(store_pokemon.value)
}

const getPokemons = async () => {
  const response = await axios.get(`https://pokeapi.co/api/v2/pokemon?limit=10&offset=${index_pokemons.value}`)
  const temp_pokemons = []
  for (let index = 0; index < response.data.results.length; index++) {
    const result = response.data.results[index]
    const pokemon = await axios.get(result.url)
    temp_pokemons.push(pokemon.data)
  }

  all_pokemons.value = temp_pokemons
}

const Details = async (id) => {
  pokemon_numero.value = id
  await searchPokemon()
  page.value = 'information'
  console.log(id)
}

// const searchBarrie = async () => {
//   const response = await axios.get(`https://pokeapi.co/api/v2/berry/${barrie_number.value}`)

//   store_barrie.value = {
//     name: response.data.name,
//     natural_gift_power: response.data.natural_gift_power,
//     size: response.data.size
//   }


// }
</script>
<style scoped>
</style>