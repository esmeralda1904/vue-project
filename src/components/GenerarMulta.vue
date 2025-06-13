<template>
  <div>
    <form @submit.prevent="guardarMulta">
      <div>
        <label>Departamento</label>
        <input type="text" v-model="departamento" readonly />
      </div>

      <div>
        <label>Monto</label>
        <input type="number" v-model="monto" required />
      </div>

      <div>
        <label>Motivo</label>
        <input type="text" v-model="motivo" required />
      </div>

      <div>
        <label>Fecha</label>
        <input type="date" v-model="fecha" required />
      </div>

      <transition name="fade">
        <div>
          <button v-if="!loading" type="submit">Guardar</button>
          <button v-else type="button" disabled>Cargando...</button>
        </div>
      </transition>
    </form>

    <transition name="modal-fade">
      <div v-if="mostrarModal" class="modal-overlay" @click.self="cerrarModal">
        <div class="modal-content">
          <p>{{ mensaje }}</p>
          <button @click="cerrarModal">Cerrar</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const departamento = ref('Recursos Humanos')
const monto = ref('')
const motivo = ref('')
const fecha = ref('')
const loading = ref(false)
const mostrarModal = ref(false)
const mensaje = ref('')

const guardarMulta = async () => {
  loading.value = true
  try {
    const res = await fetch('http://localhost:8000/api/multas', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        departamento: departamento.value,
        monto: monto.value,
        motivo: motivo.value,
        fecha: fecha.value,
      }),
    })

    if (!res.ok) throw new Error('Error al guardar multa')

    const data = await res.json()
    mensaje.value = `Multa guardada con éxito: ID ${data.id}`
    mostrarModal.value = true

    // Limpia formulario
    monto.value = ''
    motivo.value = ''
    fecha.value = ''
  } catch (error) {
    mensaje.value = `Error: ${error.message}`
    mostrarModal.value = true
  } finally {
    loading.value = false
  }
}

const cerrarModal = () => {
  mostrarModal.value = false
}
</script>

<style scoped>
/* Transiciones */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

/* Modal fade */
.modal-fade-enter-active, .modal-fade-leave-active {
  transition: opacity 0.3s ease;
}
.modal-fade-enter-from, .modal-fade-leave-to {
  opacity: 0;
}

/* Modal estilos */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0,0,0,0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-content {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  max-width: 300px;
  text-align: center;
}
form div {
  margin-bottom: 1rem;
  display: flex;
  flex-direction: column;
}

label {
  font-weight: 600;
  margin-bottom: 0.3rem;
  color: #374151; /* gris oscuro */
}

input[type="text"],
input[type="number"],
input[type="date"] {
  padding: 0.5rem 0.75rem;
  border: 1px solid #d1d5db; /* gris claro */
  border-radius: 6px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

input[type="text"]:focus,
input[type="number"]:focus,
input[type="date"]:focus {
  outline: none;
  border-color: #2563eb; /* azul */
  box-shadow: 0 0 3px rgba(37, 99, 235, 0.5);
}

button {
  padding: 0.6rem 1.2rem;
  background-color: #2563eb;
  border: none;
  border-radius: 6px;
  color: white;
  font-weight: 600;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease;
}

button:hover:not(:disabled) {
  background-color: #1e40af;
}

button:disabled {
  background-color: #94a3b8;
  cursor: not-allowed;
}

</style>
