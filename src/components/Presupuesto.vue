<script setup>
import { ref } from 'vue'
import Alerta from './Alerta.vue'

const presupuesto = ref('')
const error = ref('')

const emit = defineEmits(['definir-presupuesto'])

const handleSubmit = () => {
  if (presupuesto.value <= 0) {
    error.value = 'Presupuesto no valido'

    setTimeout(() => {
      error.value = ''
    }, 3000)

    return
  }

  emit('definir-presupuesto', presupuesto.value)
}
</script>

<template>
  <form class="presupuesto" @submit.prevent="handleSubmit">
    <Alerta v-if="error.length > 0">{{ error }}</Alerta>
    <div class="campo">
      <label for="nuevo-presupuesto">Definir presupuesto</label>
      <input
        type="number"
        min="0"
        max="999999"
        class="nuevo-presupuesto"
        v-model="presupuesto"
        id="nuevo-presupuesto"
        placeholder="ej: 100"
      />
    </div>
    <input type="submit" value="Ingresar" />
  </form>
</template>

<style scoped>
.presupuesto {
  width: 100%;
}
.campo {
  display: grid;
  gap: 2rem;
}
.presupuesto label {
  font-size: 2.2rem;
  text-align: center;
  color: var(--azul);
}
.presupuesto input[type='number'] {
  background-color: var(--gris-claro);
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2.2rem;
  text-align: center;
}
.presupuesto input[type='number']::placeholder {
  text-align: center;
}
.presupuesto input[type='submit'] {
  background-color: var(--azul);
  border: none;
  padding: 1rem;
  text-align: center;
  font-size: 2rem;
  margin-top: 2rem;
  color: var(--blanco);
  font-weight: 900;
  width: 100%;
  transition: background-color 300ms ease;
}
.presupuesto input[type='submit']:hover {
  background-color: #1048a4;
  cursor: pointer;
}
</style>
