<script setup>
defineProps({
  filtro: {
    type: String,
    required: true
  }
})
defineEmits(['update:filtro'])
</script>

<template>
  <div class="filtros sombra contenedor">
    <form>
      <div class="campo">
        <label for="filtro"> Filtrar Gastos </label>

        <select
          name="filtro"
          id="filtro"
          :value="filtro"
          @input="$emit('update:filtro', $event.target.value)"
        >
          <option value="">-- seleccione --</option>
          <option value="ahorro">ahorro</option>
          <option value="comida">comida</option>
          <option value="casa">casa</option>
          <option value="gastos">gastos</option>
          <option value="salud">salud</option>
          <option value="ocio">ocio</option>
          <option value="suscripciones">suscripciones</option>
        </select>
      </div>
    </form>
  </div>
</template>

<style scoped>
.filtros {
  margin-top: 10rem;
}
.filtros .campo {
  display: flex;
  align-items: center;
  gap: 2rem;
}
.filtros label {
  font-size: 3rem;
  font-weight: 900;
  color: var(--gris-oscuro);
}
.filtros select {
  flex: 1;
  padding: 1rem;
  border: none;
  border-radius: 1rem;
  background-color: var(--gris-claro);
  text-align: center;
}
</style>
