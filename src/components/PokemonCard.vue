<script setup>
defineProps({
  pokemon: {
    type: Object,
    required: true
  }
})
</script>

<template>
  <div class="card">
    <span class="numero">#{{ String(pokemon.id).padStart(3, '0') }}</span>
    <img 
      :src="pokemon.sprites.other['official-artwork'].front_default || pokemon.sprites.front_default" 
      :alt="pokemon.name"
      class="imagem"
    />
    <h3>{{ pokemon.name }}</h3>
    <div class="tipos">
      <span 
        v-for="tipo in pokemon.types" 
        :key="tipo.type.name"
        :class="['tipo', tipo.type.name]"
      >
        {{ tipo.type.name }}
      </span>
    </div>
  </div>
</template>

<style scoped>
.card {
  background: white;
  border-radius: 15px;
  padding: 20px 15px;
  text-align: center;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.numero {
  color: #999;
  font-size: 0.85rem;
  font-weight: bold;
}

.imagem {
  width: 120px;
  height: 120px;
  object-fit: contain;
}

h3 {
  text-transform: capitalize;
  margin: 10px 0;
  color: #333;
  font-size: 1.1rem;
}

.tipos {
  display: flex;
  gap: 5px;
  justify-content: center;
  flex-wrap: wrap;
}

.tipo {
  padding: 3px 10px;
  border-radius: 10px;
  color: white;
  font-size: 0.75rem;
  text-transform: capitalize;
}

.tipo.fire { background: #f08030; }
.tipo.water { background: #6890f0; }
.tipo.grass { background: #78c850; }
.tipo.electric { background: #f8d030; color: #333; }
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
</style>
