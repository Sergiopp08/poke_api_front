<template>
  <div>
      <h1 class="title">POKEDEX</h1>
      
      <!-- Barra de búsqueda y botón de búsqueda -->
      <div class="search-bar">
          <input type="text" placeholder="Buscar Pokémon..." class="search-input" />
          <button class="search-button">Buscar</button>
      </div>

      <ul class="pokemon-list" v-if="pokemonData.length">
          <li v-for="(pokemon, index) in pokemonData" :key="index" class="pokemon-item">
              <img :src="pokemon.image" :alt="pokemon.name" class="pokemon-image" />
              <p>{{ pokemon.name }}</p>
          </li>
      </ul>
  </div>
</template>

<script setup>
import { ref, onBeforeMount } from 'vue';
import "/src/assets/pokeapi.scss"

// Definición de props
const props = defineProps({
  url: {
      type: String,
      default: 'https://pokeapi.co/api/v2/pokemon/?offset=0&limit=151'
  },
});

const pokemonData = ref([]);

// Hook onBeforeMount
onBeforeMount(async () => {
  try {
      const response = await fetch(props.url);
      const data = await response.json();

      // Obtener detalles adicionales para cada Pokémon
      pokemonData.value = await Promise.all(data.results.map(async (pokemon) => {
          const res = await fetch(pokemon.url);
          const details = await res.json();
          return {
              name: pokemon.name,
              url: pokemon.url,
              image: details.sprites.front_default // URL de la imagen
          };
      }));
  } catch (error) {
      console.error("Error al obtener datos:", error);
  }
});
</script>


