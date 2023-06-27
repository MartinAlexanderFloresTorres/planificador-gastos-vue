<script setup>
import { ref } from 'vue'
import Alerta from './Alerta.vue'
import iconoCerrarModal from '../assets/img/cerrar.svg'

const error = ref('')

const props = defineProps({
  modal: {
    type: Object,
    required: true
  },
  nombre: {
    type: String,
    required: true
  },
  cantidad: {
    type: Number,
    required: true
  },
  categoria: {
    type: String,
    required: true
  },
  id: {
    type: [String, null],
    required: true
  },
  disponible: {
    type: Number,
    required: true
  }
})

const cantidadGasto = props.cantidad

const emit = defineEmits([
  'ocultar-modal',
  'guardar-gasto',
  'eliminar-gasto',
  'update:nombre',
  'update:cantidad',
  'update:categoria',
  'update:id'
])

const handleSubmit = () => {
  const { nombre, cantidad, categoria, disponible, id } = props
  if ([nombre, cantidad, categoria].includes('')) {
    error.value = 'todos los campos son obligatorios'
    setTimeout(() => {
      error.value = ''
    }, 3000)
    return
  }

  if (cantidad <= 0 || isNaN(cantidad)) {
    error.value = 'cantidad no valida'
    setTimeout(() => {
      error.value = ''
    }, 3000)
    return
  }

  if (id) {
    if (cantidad > cantidadGasto + disponible) {
      error.value = 'cantidad mayor al disponible'
      setTimeout(() => {
        error.value = ''
      }, 3000)
      return
    }
  } else if (cantidad > disponible) {
    error.value = 'cantidad mayor al disponible'
    setTimeout(() => {
      error.value = ''
    }, 3000)
    return
  }

  emit('guardar-gasto')
}
</script>

<template>
  <div class="modal">
    <div class="cerrar-modal">
      <img :src="iconoCerrarModal" alt="icono cerrar modal" @click="$emit('ocultar-modal')" />
    </div>

    <div class="contenedor contenedor-formulario" :class="[modal.animar ? 'animar' : 'cerrar']">
      <form class="nuevo-gasto" @submit.prevent="handleSubmit">
        <legend>{{ id ? 'Editar Gasto' : 'Añadir Gasto' }}</legend>
        <Alerta v-if="error.length > 0">
          {{ error }}
        </Alerta>
        <div class="campo">
          <label for="nombre">Nombre del gasto</label>
          <input
            type="text"
            name="nombre"
            id="nombre"
            placeholder="ej: Comida"
            :value="nombre"
            @input="$emit('update:nombre', $event.target.value)"
          />
        </div>

        <div class="campo">
          <label for="cantidad">Cantidad del gasto</label>
          <input
            type="number"
            name="cantidad"
            id="cantidad"
            placeholder="ej: 300"
            :value="cantidad"
            @input="$emit('update:cantidad', Number($event.target.value))"
          />
        </div>

        <div class="campo">
          <label for="categoria">Categoria</label>
          <select
            name="categoria"
            id="categoria"
            :value="categoria"
            @input="$emit('update:categoria', $event.target.value)"
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

        <input type="submit" :value="[id ? 'Guardar gasto' : 'Añadir Gasto']" />
      </form>

      <button v-if="id" type="button" class="btn-eliminar" @click="$emit('eliminar-gasto', id)">
        Eliminar Gasto
      </button>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  background-color: rgb(0 0 0 / 0.9);
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 100;
}
.cerrar-modal {
  position: absolute;
  right: 3rem;
  top: 3rem;
}
.cerrar-modal img {
  width: 3rem;
  cursor: pointer;
}

.contenedor-formulario {
  transition-property: all;
  transition-duration: 200ms;
  transition-timing-function: ease-in;
  opacity: 0;
}
.contenedor-formulario.animar {
  opacity: 1;
}

.contenedor-formulario.cerrar {
  opacity: 0;
}

.nuevo-gasto {
  margin: 10rem auto 0 auto;
  display: grid;
  gap: 2rem;
}
.nuevo-gasto legend {
  text-align: center;
  color: var(--blanco);
  font-size: 3rem;
  font-weight: 700;
}
.campo {
  display: grid;
  gap: 2rem;
}
.nuevo-gasto input,
.nuevo-gasto select {
  background-color: var(--gris-claro);
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2.2rem;
  text-transform: capitalize;
}
.nuevo-gasto label {
  color: var(--blanco);
  font-size: 3rem;
}
.nuevo-gasto input[type='submit'] {
  background-color: var(--azul);
  color: var(--blanco);
  font-weight: 700;
  cursor: pointer;
}

.btn-eliminar {
  border: none;
  padding: 1rem;
  width: 100%;
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2.2rem;
  text-transform: capitalize;
  background-color: #ef4444;
  font-weight: 700;
  cursor: pointer;
  color: var(--blanco);
  margin-top: 20px;
  cursor: pointer;
}

.modal .alerta {
  background: #fff;
}
</style>
