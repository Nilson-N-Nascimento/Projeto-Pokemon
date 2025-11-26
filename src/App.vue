<script setup>
import { ref, computed, onMounted } from 'vue'
import PokemonCard from './components/PokemonCard.vue'

const pokemons = ref([])
const busca = ref('')
const carregando = ref(true)
const pokemonSelecionado = ref(null)

const pokemonsFiltrados = computed(() => {
  if (!busca.value) return pokemons.value
  return pokemons.value.filter(p => 
    p.name.toLowerCase().includes(busca.value.toLowerCase())
  )
})

async function carregarPokemons() {
  try {
    const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=50')
    const data = await response.json()
    
    const detalhes = await Promise.all(
      data.results.map(async (pokemon) => {
        const res = await fetch(pokemon.url)
        return res.json()
      })
    )
    
    pokemons.value = detalhes
  } catch (erro) {
    console.error('Erro ao carregar:', erro)
  } finally {
    carregando.value = false
  }
}

function selecionarPokemon(pokemon) {
  pokemonSelecionado.value = pokemon
}

function fecharModal() {
  pokemonSelecionado.value = null
}

onMounted(() => {
  carregarPokemons()
})
</script>

<template>
  <div class="container">
    <header>
      <h1>Pokédex</h1>
      <input 
        v-model="busca" 
        type="text" 
        placeholder="Buscar Pokémon..."
        class="campo-busca"
      />
    </header>

    <div v-if="carregando" class="carregando">
      Carregando Pokémon...
    </div>

    <div v-else class="grid-pokemon">
      <PokemonCard 
        v-for="pokemon in pokemonsFiltrados" 
        :key="pokemon.id"
        :pokemon="pokemon"
        @click="selecionarPokemon(pokemon)"
      />
    </div>

    <div v-if="pokemonSelecionado" class="modal-overlay" @click="fecharModal">
      <div class="modal" @click.stop>
        <button class="btn-fechar" @click="fecharModal">×</button>
        <img 
          :src="pokemonSelecionado.sprites.other['official-artwork'].front_default" 
          :alt="pokemonSelecionado.name"
          class="img-modal"
        />
        <h2>{{ pokemonSelecionado.name }}</h2>
        <div class="info-pokemon">
          <p><strong>Altura:</strong> {{ pokemonSelecionado.height / 10 }} m</p>
          <p><strong>Peso:</strong> {{ pokemonSelecionado.weight / 10 }} kg</p>
          <p><strong>Experiência Base:</strong> {{ pokemonSelecionado.base_experience }}</p>
        </div>
        <div class="tipos">
          <span 
            v-for="tipo in pokemonSelecionado.types" 
            :key="tipo.type.name"
            :class="['tipo', tipo.type.name]"
          >
            {{ tipo.type.name }}
          </span>
        </div>
        <div class="stats">
          <h3>Estatísticas</h3>
          <div v-for="stat in pokemonSelecionado.stats" :key="stat.stat.name" class="stat-item">
            <span class="stat-nome">{{ stat.stat.name }}</span>
            <div class="stat-barra">
              <div class="stat-valor" :style="{ width: Math.min(stat.base_stat, 100) + '%' }"></div>
            </div>
            <span class="stat-numero">{{ stat.base_stat }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 30px;
}

header h1 {
  color: #ffffff;
  font-size: 2.5rem;
  margin-bottom: 20px;
}

.campo-busca {
  width: 100%;
  max-width: 400px;
  padding: 12px 20px;
  font-size: 1rem;
  border: 2px solid #ddd;
  border-radius: 25px;
  outline: none;
}

.campo-busca:focus {
  border-color: #e3350d;
}

.carregando {
  text-align: center;
  font-size: 1.2rem;
  color: #666;
  padding: 50px;
}

.grid-pokemon {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
  gap: 20px;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  border-radius: 20px;
  padding: 30px;
  max-width: 400px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  text-align: center;
}

.btn-fechar {
  position: absolute;
  top: 10px;
  right: 15px;
  background: none;
  border: none;
  font-size: 2rem;
  cursor: pointer;
  color: #666;
}

.img-modal {
  width: 200px;
  height: 200px;
}

.modal h2 {
  text-transform: capitalize;
  color: #333;
  margin: 10px 0;
}

.info-pokemon {
  margin: 15px 0;
  color: #555;
}

.info-pokemon p {
  margin: 5px 0;
}

.tipos {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin: 15px 0;
}

.tipo {
  padding: 5px 15px;
  border-radius: 15px;
  color: white;
  font-size: 0.9rem;
  text-transform: capitalize;
}

.tipo.fire { background: #f08030; }
.tipo.water { background: #6890f0; }
.tipo.grass { background: #78c850; }
.tipo.electric { background: #f8d030; }
.tipo.psychic { background: #f85888; }
.tipo.ice { background: #98d8d8; }
.tipo.dragon { background: #7038f8; }
.tipo.dark { background: #705848; }
.tipo.fairy { background: #ee99ac; }
.tipo.normal { background: #a8a878; }
.tipo.fighting { background: #c03028; }
.tipo.flying { background: #a890f0; }
.tipo.poison { background: #a040a0; }
.tipo.ground { background: #e0c068; }
.tipo.rock { background: #b8a038; }
.tipo.bug { background: #a8b820; }
.tipo.ghost { background: #705898; }
.tipo.steel { background: #b8b8d0; }

.stats {
  margin-top: 20px;
  text-align: left;
}

.stats h3 {
  text-align: center;
  margin-bottom: 15px;
  color: #333;
}

.stat-item {
  display: flex;
  align-items: center;
  margin: 8px 0;
  gap: 10px;
}

.stat-nome {
  width: 120px;
  font-size: 0.85rem;
  text-transform: capitalize;
  color: #555;
}

.stat-barra {
  flex: 1;
  height: 8px;
  background: #eee;
  border-radius: 4px;
  overflow: hidden;
}

.stat-valor {
  height: 100%;
  background: #e3350d;
  border-radius: 4px;
}

.stat-numero {
  width: 35px;
  text-align: right;
  font-weight: bold;
  color: #333;
}
</style>
