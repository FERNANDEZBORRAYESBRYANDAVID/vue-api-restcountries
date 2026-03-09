<script setup>
import { ref, onMounted } from 'vue'

const paises = ref([])
const busqueda = ref('')
const cargando = ref(true)
const error = ref(null)

onMounted(async () => {
  try {
    const respuesta = await fetch(
      'https://restcountries.com/v3.1/all?fields=name,flags,capital,region,subregion,population,languages,currencies',
    )

    const datos = await respuesta.json()
    paises.value = datos
    cargando.value = false
  } catch (e) {
    error.value = 'No fue posible obtener los datos de la API'
    cargando.value = false
  }
})

const paisesFiltrados = () => {
  return paises.value.filter((pais) =>
    pais.name.common.toLowerCase().includes(busqueda.value.toLowerCase()),
  )
}
</script>

<template>
  <div class="container">
    <h1>🌍 Explorador de Países</h1>

    <ul>
      <p>Integrantes del grupo:</p>
      <li>Bryan David Fernandez Borrayes</li>
      <li>Angel Daneil Toala Ortiz</li>
      <li>Adrian Mundo Zarate</li>
    </ul>

    <input v-model="busqueda" placeholder="Buscar país..." class="buscador" />

    <p v-if="cargando" class="mensaje">Cargando países...</p>

    <p v-if="error" class="mensaje error">{{ error }}</p>

    <div class="grid" v-if="!cargando">
      <div class="card" v-for="pais in paisesFiltrados()" :key="pais.name.common">
        <img :src="pais.flags.png" class="bandera" />

        <h2>{{ pais.name.common }}</h2>

        <p><strong>Capital:</strong> {{ pais.capital?.[0] }}</p>

        <p><strong>Región:</strong> {{ pais.region }}</p>

        <p><strong>Población:</strong> {{ pais.population.toLocaleString() }}</p>
      </div>
    </div>
  </div>
</template>

<style>
body {
  font-family: Arial, Helvetica, sans-serif;
  background: #f5f5f5;
  margin: 0;
}

.container {
  max-width: 1200px;
  margin: auto;
  padding: 20px;
  text-align: center;
}

h1 {
  margin-bottom: 20px;
}

.buscador {
  padding: 10px;
  width: 300px;
  border-radius: 8px;
  border: 1px solid #ccc;
  margin-bottom: 30px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 20px;
}

.card {
  background: white;
  border-radius: 10px;
  padding: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s;
}

.card:hover {
  transform: scale(1.05);
}

.bandera {
  width: 100%;
  height: 120px;
  object-fit: cover;
  border-radius: 6px;
  margin-bottom: 10px;
}

.mensaje {
  font-size: 18px;
}

.error {
  color: red;
}
</style>
