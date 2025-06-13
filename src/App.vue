<template>
  <div id="app">
    <header class="header">
      <nav class="nav">
        <button 
          :class="{'active': view === 'menu'}" 
          @click="view = 'menu'"
          aria-label="Menú"
        >
          Menú
        </button>

        <button 
          :class="{'active': view === 'perfil'}" 
          @click="view = 'perfil'"
          aria-label="Perfil"
        >
          Perfil
        </button>

        <button 
          :class="{'active': view === 'multas'}" 
          @click="view = 'multas'"
          aria-label="Multas"
        >
          Multas
        </button>

        <button 
          :class="{'active': view === 'generar'}" 
          @click="view = 'generar'" 
          aria-label="Generar Multa"
        >
          Generar Multa
        </button>

        <div class="notificacion-wrapper" @click="irAMultas" role="button" tabindex="0" aria-label="Notificaciones de multas">
          <span :class="['campana', {'active': view === 'multas'}]">🔔</span>
          <span v-if="nuevaMulta" class="badge">1</span>
        </div>
      </nav>
    </header>

    <main class="main-content">
      <component :is="currentView" @multas-actualizadas="actualizarNotificaciones" />
    </main>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import Menu from './components/Menu.vue'
import Perfil from './components/perfil.vue'
import Multas from './components/Multas.vue'
import GenerarMulta from './components/GenerarMulta.vue'

const view = ref('menu')
const currentCount = ref(0)
const nuevaMulta = ref(false)
const departamento = 'Recursos Humanos'

const currentView = computed(() => {
  switch (view.value) {
    case 'perfil': return Perfil
    case 'multas': return Multas
    case 'generar': return GenerarMulta
    default: return Menu
  }
})

const actualizarNotificaciones = async () => {
  try {
    const res = await fetch(`http://localhost:8000/api/notificaciones/${departamento}`)
    const data = await res.json()
    if (data.length > currentCount.value) {
      nuevaMulta.value = true
    }
    currentCount.value = data.length
  } catch (error) {
    console.error('Error al obtener notificaciones:', error)
  }
}

const irAMultas = () => {
  view.value = 'multas'
  nuevaMulta.value = false
}

onMounted(() => {
  actualizarNotificaciones()
  setInterval(actualizarNotificaciones, 5000)
})
</script>

<style scoped>
* {
  box-sizing: border-box;
}

body, html, #app {
  margin: 0;
  padding: 0;
  height: 100%;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: #f3f4f6;
  color: #1f2937;
}

.header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: #2563eb;
  color: white;
  box-shadow: 0 2px 8px rgb(37 99 235 / 0.4);
  z-index: 1000;
}

.nav {
  max-width: 960px;
  margin: 0 auto;
  padding: 0.75rem 1rem;
  display: flex;
  align-items: center;
  gap: 1rem;
}

.nav button {
  background: transparent;
  border: none;
  color: white;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
  transition: background-color 0.3s ease;
}

.nav button:hover,
.nav button.active {
  background: #1e40af;
  outline: none;
}

.notificacion-wrapper {
  position: relative;
  cursor: pointer;
  user-select: none;
  font-size: 1.5rem;
  display: flex;
  align-items: center;
}

.campana {
  color: white;
  transition: color 0.3s ease;
}

.campana.active {
  color: #93c5fd;
}

.badge {
  position: absolute;
  top: 0;
  right: -6px;
  background-color: #dc2626;
  color: white;
  font-size: 0.65rem;
  font-weight: 700;
  border-radius: 9999px;
  width: 16px;
  height: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 0 2px #fff;
}

.main-content {
  max-width: 960px;
  margin: 80px auto 2rem;
  padding: 1rem 1.5rem;
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgb(0 0 0 / 0.05);
  min-height: calc(100vh - 100px);
  color: #1e293b;
}
</style>
