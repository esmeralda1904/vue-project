<template>
  <div class="container">
    <h2 class="titulo">Multas </h2>
    <div v-if="multas.length === 0" class="sin-multas">No hay multas.</div>
    <ul class="lista">
      <li v-for="multa in multas" :key="multa.id" class="tarjeta">
        <div class="fecha">{{ multa.fecha }}</div>
        <div class="contenido">
          <strong>Motivo:</strong> {{ multa.motivo }}<br />
          <strong>Monto:</strong> ${{ multa.monto }}
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const departamento = 'Recursos Humanos'
const multas = ref([])
let interval = null

const cargarMultas = () => {
  fetch(`http://localhost:8000/api/notificaciones/${departamento}`)
    .then(res => res.json())
    .then(data => {
      multas.value = data
    })
    .catch(console.error)
}

onMounted(() => {
  cargarMultas()
  interval = setInterval(cargarMultas, 5000)
})

onBeforeUnmount(() => {
  clearInterval(interval)
})
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 30px auto;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f9fafb;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 0 12px rgba(0, 0, 0, 0.1);
}

.titulo {
  text-align: center;
  color: #1f2937;
  margin-bottom: 20px;
}

.lista {
  list-style: none;
  padding: 0;
}

.tarjeta {
  background-color: #e5e7eb;
  border-left: 6px solid #2563eb;
  padding: 15px;
  margin-bottom: 15px;
  border-radius: 8px;
  transition: background 0.3s ease;
}

.tarjeta:hover {
  background-color: #dbeafe;
}

.fecha {
  font-size: 0.9rem;
  color: #4b5563;
  margin-bottom: 6px;
}

.contenido {
  font-size: 1rem;
  color: #111827;
}

.sin-multas {
  text-align: center;
  color: #6b7280;
  margin-top: 20px;
}
</style>
