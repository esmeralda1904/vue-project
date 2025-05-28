<template>
  <div>
    <h2>Multas del departamento: {{ departamento }}</h2>
    <ul>
      <li v-for="multa in multas" :key="multa.id">
        {{ multa.fecha }} - {{ multa.motivo }} (${{ multa.monto }})
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      departamento: 'Recursos Humanos',
      multas: [],
      interval: null,
    }
  },
  methods: {
    cargarMultas() {
      fetch(`http://localhost:8000/api/notificaciones/${this.departamento}`)
        .then(res => res.json())
        .then(data => {
          this.multas = data;
        })
        .catch(console.error);
    }
  },
  mounted() {
    this.cargarMultas();
    this.interval = setInterval(this.cargarMultas, 5000);
  },
  beforeUnmount() {
    clearInterval(this.interval);
  }
}
</script>
