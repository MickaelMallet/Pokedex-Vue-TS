<template>
  <div class="pokemon" v-if="pokemon">
    <h3 class="pokemon__title">{{ pokemon.name }}</h3>
    <img class="pokemon__img" :src="pokemon.sprites.front_default" />
    <div class="pokemon__types">
      <span v-for="t in pokemon.types" :key="t.slot">
        {{ t.type.name }}
      </span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
// J'ai exporter mon interface Pokémon dans un autre fichier afin de rester lisible et de pouvoir réutiliser ce type ailleurs si besoin
import { Pokemon } from './../../@types/pokemon'

// On définir les propriétés d'un composant vue, on utilise defineProps
const props = defineProps<{
  name: string;
  url: string
}>()

const pokemon = ref<Pokemon>();

async function getPokemon() {
  const response = await fetch(props.url);
  pokemon.value = await response.json();
}

getPokemon();
</script>

<style scoped>
.pokemon {
  padding: 1rem;
  width: 200px;
  border: 2px solid #F0F;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pokemon__title {
  color: #F0F;
  font-weight: bold;
  font-size: 1.5rem;
}

.pokemon__img {
  max-width: 100%;
}

.pokemon__types {
  display: flex;
  justify-content: center;
  gap: 2rem;
}
</style>
