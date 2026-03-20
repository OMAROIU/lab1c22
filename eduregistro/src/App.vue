<template>
  <div class="app">

    <AppHeader :total="estudiantes.length" />

    <ToastMsg :toast="toast" />

    <ResumenCards
      v-if="estudiantes.length > 0"
      :promedio="promedioGeneral"
      :aprobados="aprobados"
      :reprobados="reprobados"
      :total="estudiantes.length"
      :claseNota="claseNota"
    />

    <FormularioEstudiante
      :form="form"
      :errores="errores"
      :modoEdicion="modoEdicion"
      :materias="materias"
      :secciones="secciones"
      :claseNota="claseNota"
      @guardar="guardar"
      @limpiar="limpiarForm"
    />

    <TablaEstudiantes
      v-if="estudiantes.length > 0"
      :estudiantes="estudiantesFiltrados"
      :filtros="filtros"
      :filtroActivo="filtroActivo"
      :claseNota="claseNota"
      @buscar="busqueda = $event"
      @filtrar="filtroActivo = $event"
      @editar="editar"
      @eliminar="eliminar"
    />

  </div>
</template>

<script setup>
import { ref, reactive, computed, watch } from 'vue'
import AppHeader          from './components/AppHeader.vue'
import ToastMsg           from './components/ToastMsg.vue'
import ResumenCards       from './components/ResumenCards.vue'
import FormularioEstudiante from './components/FormularioEstudiante.vue'
import TablaEstudiantes   from './components/TablaEstudiantes.vue'

// ── VARIABLES REACTIVAS ──────────────────────────────────
const estudiantes  = ref([])
const busqueda     = ref('')
const filtroActivo = ref('todos')
const modoEdicion  = ref(false)
const editId       = ref(null)

const toast = reactive({ visible: false, msg: '', tipo: 'ok' })

const errores = reactive({ carnet: '', nombre: '', materia: '', nota: '' })

const form = reactive({
  carnet: '', nombre: '', materia: '',
  seccion: 'A', nota: 5.0, observacion: ''
})

// ── DATOS ESTÁTICOS ──────────────────────────────────────
const materias = [
  'Programación Computacional IV',
  'Bases de Datos II',
  'Redes de Computadoras',
  'Ingeniería de Software',
  'Matemática Discreta',
  'Sistemas Operativos',
]

const secciones = ['A', 'B', 'C', 'D']

const filtros = [
  { label: 'Todos',      valor: 'todos'       },
  { label: 'Aprobados',  valor: 'aprobado'    },
  { label: 'En riesgo',  valor: 'advertencia' },
  { label: 'Reprobados', valor: 'reprobado'   },
]

// ── COMPUTADAS ───────────────────────────────────────────
const promedioGeneral = computed(() => {
  if (!estudiantes.value.length) return 0
  return estudiantes.value.reduce((s, e) => s + Number(e.nota), 0) / estudiantes.value.length
})

const aprobados  = computed(() => estudiantes.value.filter(e => Number(e.nota) >= 6).length)
const reprobados = computed(() => estudiantes.value.filter(e => Number(e.nota) < 6).length)

const estudiantesFiltrados = computed(() => {
  let lista = [...estudiantes.value]
  const q = busqueda.value.trim().toLowerCase()
  if (q) lista = lista.filter(e =>
    e.nombre.toLowerCase().includes(q) ||
    e.carnet.toLowerCase().includes(q) ||
    e.materia.toLowerCase().includes(q)
  )
  if (filtroActivo.value !== 'todos')
    lista = lista.filter(e => claseNota(Number(e.nota)) === filtroActivo.value)
  return lista
})

// ── HELPERS ──────────────────────────────────────────────
function claseNota(n) {
  if (n >= 7) return 'aprobado'
  if (n >= 6) return 'advertencia'
  return 'reprobado'
}

// ── VALIDACIÓN ───────────────────────────────────────────
function validar() {
  let ok = true
  const n = Number(form.nota)

  if (!form.carnet.trim()) {
    errores.carnet = 'El carnet es obligatorio.'; ok = false
  } else if (!/^[A-Za-z0-9\-]+$/.test(form.carnet.trim())) {
    errores.carnet = 'Solo letras, números y guiones.'; ok = false
  } else if (!modoEdicion.value && estudiantes.value.some(
    e => e.carnet.toLowerCase() === form.carnet.trim().toLowerCase()
  )) {
    errores.carnet = 'Carnet ya registrado.'; ok = false
  }

  if (!form.nombre.trim()) {
    errores.nombre = 'El nombre es obligatorio.'; ok = false
  } else if (form.nombre.trim().length < 5) {
    errores.nombre = 'Nombre demasiado corto.'; ok = false
  }

  if (!form.materia) {
    errores.materia = 'Selecciona una materia.'; ok = false
  }

  if (isNaN(n) || n < 0 || n > 10) {
    errores.nota = 'La nota debe estar entre 0 y 10.'; ok = false
  }

  return ok
}

// ── MÉTODOS ──────────────────────────────────────────────
function guardar() {
  if (!validar()) return

  if (modoEdicion.value) {
    const idx = estudiantes.value.findIndex(e => e.id === editId.value)
    if (idx !== -1) {
      estudiantes.value[idx] = {
        ...estudiantes.value[idx],
        nombre:      form.nombre.trim(),
        materia:     form.materia,
        seccion:     form.seccion,
        nota:        Number(form.nota),
        observacion: form.observacion.trim(),
      }
    }
    mostrarToast('Registro actualizado.', 'ok')
  } else {
    estudiantes.value.push({
      id:          Date.now(),
      carnet:      form.carnet.trim().toUpperCase(),
      nombre:      form.nombre.trim(),
      materia:     form.materia,
      seccion:     form.seccion,
      nota:        Number(form.nota),
      observacion: form.observacion.trim(),
    })
    mostrarToast('Estudiante registrado.', 'ok')
  }
  limpiarForm()
}

function eliminar(id) {
  estudiantes.value = estudiantes.value.filter(e => e.id !== id)
  mostrarToast('Registro eliminado.', 'warn')
}

function editar(est) {
  Object.assign(form, {
    carnet: est.carnet, nombre: est.nombre,
    materia: est.materia, seccion: est.seccion,
    nota: est.nota, observacion: est.observacion,
  })
  modoEdicion.value = true
  editId.value = est.id
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function limpiarForm() {
  Object.assign(form, { carnet: '', nombre: '', materia: '', seccion: 'A', nota: 5.0, observacion: '' })
  Object.assign(errores, { carnet: '', nombre: '', materia: '', nota: '' })
  modoEdicion.value = false
  editId.value = null
}

function mostrarToast(msg, tipo) {
  toast.msg = msg; toast.tipo = tipo; toast.visible = true
  setTimeout(() => { toast.visible = false }, 2500)
}

watch(() => form.materia, () => { errores.materia = '' })
</script>

<style scoped>
.app {
  max-width: 820px;
  margin: 0 auto;
  padding: 2rem 1.25rem 4rem;
}
</style>