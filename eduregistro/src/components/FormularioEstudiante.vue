<template>
  <section class="card">
    <h2 class="card-title">
      {{ modoEdicion ? 'Editar registro' : 'Nuevo registro' }}
    </h2>

    <div class="form-grid">

      <!-- Carnet -->
      <div class="field">
        <label>Carnet *</label>
        <!--
          v-model: enlace bidireccional con form.carnet
          v-bind (:class): borde rojo si hay error de validación
          @input: limpia el mensaje de error mientras se escribe
        -->
        <input
          v-model="form.carnet"
          :class="{ 'input-err': errores.carnet }"
          type="text"
          placeholder="EJ2025-001"
          @input="errores.carnet = ''"
          maxlength="15"
        />
        <span class="err-msg">{{ errores.carnet }}</span>
      </div>

      <!-- Nombre -->
      <div class="field">
        <label>Nombre completo *</label>
        <input
          v-model="form.nombre"
          :class="{ 'input-err': errores.nombre }"
          type="text"
          placeholder="Apellido, Nombre"
          @input="errores.nombre = ''"
          maxlength="60"
        />
        <span class="err-msg">{{ errores.nombre }}</span>
      </div>

      <!-- Materia -->
      <div class="field">
        <label>Materia *</label>
        <!--
          v-for: genera las <option> dinámicamente desde el array materias[]
        -->
        <select
          v-model="form.materia"
          :class="{ 'input-err': errores.materia }"
          @change="errores.materia = ''"
        >
          <option value="" disabled>Seleccionar...</option>
          <option v-for="m in materias" :key="m" :value="m">{{ m }}</option>
        </select>
        <span class="err-msg">{{ errores.materia }}</span>
      </div>

      <!-- Sección -->
      <div class="field">
        <label>Sección</label>
        <select v-model="form.seccion">
          <option v-for="s in secciones" :key="s" :value="s">{{ s }}</option>
        </select>
      </div>

      <!-- Nota -->
      <div class="field field-full">
        <label>
          Nota <strong>{{ Number(form.nota).toFixed(1) }}</strong> / 10
        </label>
        <div class="slider-row">
          <!-- slider y número apuntan al mismo v-model -->
          <input type="range" v-model="form.nota" min="0" max="10" step="0.1" />
          <input
            v-model="form.nota"
            :class="{ 'input-err': errores.nota }"
            type="number"
            min="0" max="10" step="0.1"
            @input="errores.nota = ''"
          />
        </div>
        <span class="err-msg">{{ errores.nota }}</span>
      </div>

      <!-- Observación -->
      <div class="field field-full">
        <label>Observación <span class="opt">(opcional)</span></label>
        <input
          v-model="form.observacion"
          type="text"
          placeholder="Ej: Examen pendiente..."
          maxlength="80"
        />
      </div>

      <!-- Botones -->
      <div class="btn-row field-full">
        <!-- @click: dispara el evento 'guardar' hacia App.vue -->
        <button class="btn btn-primary" @click="$emit('guardar')">
          {{ modoEdicion ? 'Actualizar' : 'Agregar' }}
        </button>
        <!-- v-if: solo aparece cuando se está editando -->
        <button class="btn btn-ghost" v-if="modoEdicion" @click="$emit('limpiar')">
          Cancelar
        </button>
      </div>

    </div>
  </section>
</template>

<script setup>
defineProps({
  form:        { type: Object,   required: true },
  errores:     { type: Object,   required: true },
  modoEdicion: { type: Boolean,  required: true },
  materias:    { type: Array,    required: true },
  secciones:   { type: Array,    required: true },
  claseNota:   { type: Function, required: true },
})

defineEmits(['guardar', 'limpiar'])
</script>

<style scoped>
.card       { background: #fff; border: 1px solid #e0e0e0; border-radius: 6px; padding: 1.5rem; margin-bottom: 1rem; }
.card-title { font-size: .88rem; font-weight: 600; margin-bottom: 1.25rem; }

.form-grid   { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; }
.field       { display: flex; flex-direction: column; gap: .28rem; }
.field-full  { grid-column: 1 / -1; }

label        { font-size: .7rem; font-weight: 500; color: #555; }
.opt         { font-weight: 400; color: #bbb; }

input[type="text"],
input[type="number"],
select {
  width: 100%;
  padding: .48rem .65rem;
  border: 1px solid #e0e0e0;
  border-radius: 5px;
  font-size: .83rem;
  color: #111;
  background: #fff;
  outline: none;
  transition: border-color .15s;
}
input:focus, select:focus { border-color: #555; }
.input-err                { border-color: #dc2626 !important; }
.err-msg { font-size: .63rem; color: #dc2626; min-height: .85rem; }

.slider-row { display: flex; align-items: center; gap: .75rem; }
input[type="range"] { flex: 1; border: none; padding: 0; accent-color: #111; cursor: pointer; }
.slider-row input[type="number"] { width: 68px; flex-shrink: 0; }

.btn-row { display: flex; gap: .6rem; }
.btn { padding: .5rem 1.15rem; border-radius: 5px; font-size: .78rem; font-weight: 600; cursor: pointer; border: 1px solid; transition: all .15s; }
.btn-primary { background: #111; color: #fff; border-color: #111; }
.btn-primary:hover { background: #333; }
.btn-ghost   { background: #fff; color: #666; border-color: #e0e0e0; }
.btn-ghost:hover { border-color: #999; color: #111; }

@media (max-width: 560px) {
  .form-grid  { grid-template-columns: 1fr; }
  .field-full { grid-column: 1; }
}
</style>