<!-- La partie template va contenir le code "HTML" -->
<template>
  <div>Pokedex</div>

  <div class="btn-group">
    <button 
      class="btn"
      v-if="pokemonsData?.previous" 
      @click="urlToFetch = pokemonsData?.previous!"
    >
      Afficher les précédents
    </button>
    <button 
      class="btn"
      v-if="pokemonsData?.next"
      @click="urlToFetch = pokemonsData?.next!"
    >
      Afficher les suivants
    </button>
  </div>

  <!-- v-if permet de conditionner l'affichage d'un élément HTML -->
  <!-- Si ma variable error contient quelque chose, cela va l'afficher -->
  <div v-if="error">
    <!-- Pour afficher le contenu d'une variable il faut l'entourer de `{{ maVariable }}` -->
    {{ error }}
  </div>
  <div 
    v-if="pokemonsData" 
    class="pokemon-container" 
    :class="{
      'loading': loading
    }"
  >
    <!-- Pour mettre une variable en vuejs, on préfixe l'attribut de `:`-->
    <!-- Les composants importer dans la partie `script` peuvent être utiliser dans le template -->
    <!-- Pour faire une boucle en vue, on utilise le v-for avec un `in` -->
    <PokemonListItem 
      v-for="pokemon in pokemonsData.results" 
      :key="pokemon.name"
      :url="pokemon.url"
      :name="pokemon.name"
    />
  </div>
  <div v-else>
    Aucune donnée
  </div>
</template>

<!-- Dans la balise script sera mis le code JS (ici ts) -->
<script setup lang="ts">
import { ref, watch } from 'vue';
import PokemonListItem from './components/PokemonListItem.vue'

// On créer nos interfaces typescript pour pouvoir typer nos variables
interface Pokemon {
  name: string;
  url: string;
}

interface PokemonListApiResult {
  count: number;
  next?: string;
  previous?: string;
  results: Pokemon[]
}

// ref permet permet de créer une variable dynamique. Lorsque la valeur de la propriété value est modifier, cela va rendre de nouveau le HTML de la partie template
const pokemonsData = ref<PokemonListApiResult>();
const error = ref<string>();
const urlToFetch = ref<string>('https://pokeapi.co/api/v2/pokemon?limit=30')
const loading = ref<boolean>(false);

// Fonction qui va faire un fetch pour récupérer la liste des pokémons
async function getAllPokemons() {
  loading.value = true;
  try {
    const response = await fetch(urlToFetch.value);
    pokemonsData.value = await response.json();
  } catch(e) {
    error.value = 'Une erreur est survenue';
  }

  loading.value = false;
}

// J'appel ma fonction
getAllPokemons();

watch(urlToFetch, () => {
  getAllPokemons();
});
</script>

<style scoped>
.pokemon-container {
  display: flex;
  justify-content: center;
  gap: 2rem;
  flex-wrap: wrap;
}

.btn-group {
  display: flex;
  justify-content: center;
  margin: 1rem 0;
  gap: 2rem;
}
.btn {
  background-color: #F0F;
  border: none;
  color: #0F0;
  font-weight: bold;
  border-radius: 0.1rem;
}
@keyframes spin {
  100% {
    transform: rotate(360deg);
 }
}
.loading {
  position: relative;
  min-height: 30px;
  min-width: 30px;
}
.loading:after, .loading:before {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
}
.loading:after {
  border: 5px solid #0F0;
  border-radius: 50%;
  border-top: 5px solid #F0F;
  width: 30px;
  height: 30px;
  -webkit-animation: 3s linear infinite spin;
  animation: 3s linear infinite spin;
  right: 0;
  bottom: 0;
  margin: auto;
  z-index: 15;
}
.loading:before {
  height: 100%;
  width: 100%;
  display: block;
  background: rgba(249, 249, 249, 0.9);
  z-index: 10;
}

</style>
