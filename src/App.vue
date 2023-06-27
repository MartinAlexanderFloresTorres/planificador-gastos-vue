<script setup>
import { onMounted, reactive, ref, watch } from 'vue'
import Presupuesto from './components/Presupuesto.vue'
import ControlPresupuesto from './components/ControlPresupuesto.vue'
import Modal from './components/Modal.vue'
import Filtros from './components/Filtros.vue'
import Gasto from './components/Gasto.vue'
import { generarId } from './helpers'
import iconoNuevoGasto from './assets/img/nuevo-gasto.svg'

const presupuesto = ref(0)
const disponible = ref(0)
const gastado = ref(0)
const filtro = ref('')
const modal = reactive({
  mostrar: false,
  animar: false
})

const gastos = ref([])

const gasto = reactive({
  nombre: '',
  cantidad: 0,
  categoria: '',
  id: null,
  fecha: Date.now()
})

watch(
  gastos,
  () => {
    const totalGastos = gastos.value.reduce((total, gasto) => total + gasto.cantidad, 0)

    gastado.value = totalGastos
    disponible.value = presupuesto.value - totalGastos

    localStorage.setItem('gastos', JSON.stringify(gastos.value))
  },
  {
    deep: true
  }
)

watch(
  presupuesto,
  () => {
    localStorage.setItem('presupuesto', JSON.stringify(presupuesto.value))
  },
  {
    deep: true
  }
)

onMounted(() => {
  const presupuestoLocalStorage = JSON.parse(localStorage.getItem('presupuesto'))

  if (presupuestoLocalStorage) {
    presupuesto.value = Number(presupuestoLocalStorage)
    disponible.value = Number(presupuestoLocalStorage)
  }

  const gastosLocalStorage = JSON.parse(localStorage.getItem('gastos'))
  if (gastosLocalStorage) {
    gastos.value = gastosLocalStorage
  }
})

const definirPresupuesto = (value) => {
  presupuesto.value = value
  disponible.value = value
}

const mostrarModal = () => {
  modal.mostrar = true
  setTimeout(() => {
    modal.animar = true
  }, 200)
}

const reiniciarStateGasto = () => {
  Object.assign(gasto, {
    nombre: '',
    cantidad: 0,
    categoria: '',
    id: null,
    fecha: Date.now()
  })
}

const actualizarGasto = ({ ...gasto }) => {
  const index = gastos.value.findIndex((g) => g.id === gasto.id)
  gastos.value.splice(index, 1, gasto)
}

const eliminarGasto = (id) => {
  if (confirm('¿Estas seguro de eliminar este gasto?')) {
    const index = gastos.value.findIndex((g) => g.id === id)
    gastos.value.splice(index, 1)

    ocultarModal()
  }
}

const resetearApp = () => {
  if (confirm('¿Estas seguro de reiniciar la app?')) {
    localStorage.clear()
    presupuesto.value = 0
    disponible.value = 0
    gastado.value = 0
    gastos.value = []
  }
}

const ocultarModal = () => {
  modal.animar = false

  reiniciarStateGasto()

  setTimeout(() => {
    modal.mostrar = false
  }, 200)
}

const guardarGasto = () => {
  if (gasto.id) {
    actualizarGasto(gasto)
  } else {
    gastos.value.push({ ...gasto, id: generarId() })
  }

  ocultarModal()
}

const seleccionarGasto = (gastoValues) => {
  Object.assign(gasto, gastoValues)
  mostrarModal()
}
</script>

<template>
  <div :class="{ fijar: modal.mostrar }">
    <header>
      <h1>Planificador de gastos</h1>

      <div class="contenedor contenedor-header sombra">
        <Presupuesto @definir-presupuesto="definirPresupuesto" v-if="presupuesto === 0" />

        <ControlPresupuesto
          v-else
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
          @resetear-app="resetearApp"
        />
      </div>
    </header>

    <main v-if="presupuesto > 0">
      <Filtros v-model:filtro="filtro" />

      <div class="listado-gastos contenedor">
        <h2>
          {{
            gastos.filter((g) => g.categoria.includes(filtro)).length > 0
              ? 'Gastos'
              : 'No hay gastos'
          }}
        </h2>
        <Gasto
          v-for="gasto in gastos.filter((g) => g.categoria.includes(filtro))"
          :gasto="gasto"
          :key="gasto.id"
          @seleccionar-gasto="seleccionarGasto"
        />
      </div>

      <div class="crear-gasto">
        <img :src="iconoNuevoGasto" alt="icono nuevo gasto" @click="mostrarModal" />
      </div>

      <Modal
        v-if="modal.mostrar"
        @ocultar-modal="ocultarModal"
        @guardar-gasto="guardarGasto"
        @eliminar-gasto="eliminarGasto"
        :disponible="disponible"
        :modal="modal"
        v-model:nombre="gasto.nombre"
        v-model:cantidad="gasto.cantidad"
        v-model:categoria="gasto.categoria"
        v-model:id="gasto.id"
      />
    </main>
  </div>
</template>

<style>
:root {
  --azul: #3b82f6;
  --blanco: #fff;
  --gris-claro: #f5f5f5;
  --gris: #94a3b8;
  --gris-oscuro: #64748b;
  --negro: #000;
}
html {
  font-size: 62.5%;
  box-sizing: border-box;
}
*,
*:before,
*:after {
  box-sizing: inherit;
}
body {
  font-size: 1.6rem;
  font-family: 'Lato', sans-serif;
  background-color: var(--gris-claro);
}
h1 {
  font-size: 4rem;
}
h2 {
  font-size: 3rem;
}
.fijar {
  overflow: hidden;
  height: 100vh;
}
header {
  background-color: var(--azul);
}
header h1 {
  padding: 3rem 0;
  margin: 0;
  color: var(--blanco);
  text-align: center;
}
.contenedor {
  width: 90%;
  max-width: 80rem;
  margin: 0 auto;
}
.contenedor-header {
  margin-top: -5rem;
  transform: translateY(5rem);
  padding: 5rem;
}
.sombra {
  box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
  background-color: var(--blanco);
  border-radius: 1.2rem;
  padding: 5rem;
}

.crear-gasto {
  position: fixed;
  bottom: 5rem;
  right: 5rem;
}
.crear-gasto img {
  width: 5rem;
  cursor: pointer;
}
.listado-gastos {
  margin-top: 10rem;
}
.listado-gastos h2 {
  font-weight: 900;
  color: var(--gris-oscuro);
}
</style>
