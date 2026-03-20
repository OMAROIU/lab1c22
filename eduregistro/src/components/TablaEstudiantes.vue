<template>
  <section class="card">

    <div class="list-top">
      <h2 class="card-title">Estudiantes</h2>
      <div class="toolbar">
        <!--
          @input: emite el texto de búsqueda hacia App.vue
        -->
        <input
          type="text"
          class="search"
          placeholder="Buscar..."
          @input="$emit('buscar', $event.target.value)"
        />
        <div class="filters">
          <!--
            v-for: genera los botones de filtro desde el array filtros[]
            v-bind (:class): resalta el botón del filtro activo
            @click: emite el filtro seleccionado hacia App.vue
          -->
          <button
            v-for="f in filtros"
            :key="f.valor"
            :class="['fbtn', filtroActivo === f.valor ? 'fbtn-on' : '']"
            @click="$emit('filtrar', f.valor)"
          >{{ f.label }}</button>
        </div>
      </div>
    </div>

    <table class="table">
      <thead>
        <tr>
          <th>Carnet</th>
          <th>Nombre</th>
          <th>Materia</th>
          <th>Secc.</th>
          <th>Nota</th>
          <th>Observación</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <!-- v-if: fila vacía cuando no hay resultados del filtro/búsqueda -->
        <tr v-if="estudiantes.length === 0">
          <td colspan="7" class="empty">Sin resultados</td>
        </tr>
        <!--
          v-for: una fila por cada estudiante de la lista filtrada
        -->
        <tr v-for="est in estudiantes" :key="est.id">
          <td class="td-mono">{{ est.carnet }}</td>
          <td>{{ est.nombre }}</td>
          <td class="td-sm">{{ est.materia }}</td>
          <td>{{ est.seccion }}</td>
          <td>
            <!-- v-bind (:class): color del badge según la nota -->
            <span :class="['badge', claseNota(Number(est.nota))]">
              {{ Number(est.nota).toFixed(1) }}
            </span>
          </td>
          <td class="td-obs">{{ est.observacion || '—' }}</td>
          <td class="td-act">
            <!-- @click: emite el evento editar con el objeto estudiante -->
            <button class="act" @click="$emit('editar', est)">✎</button>
            <!-- @click: emite el evento eliminar con el id -->
            <button class="act red" @click="$emit('eliminar', est.id)">✕</button>
          </td>
        </tr>
      </tbody>
    </table>

  </section>
</template>

<script setup>
defineProps({
  estudiantes:  { type: Array,    required: true },
  filtros:      { type: Array,    required: true },
  filtroActivo: { type: String,   required: true },
  claseNota:    { type: Function, required: true },
})

defineEmits(['buscar', 'filtrar', 'editar', 'eliminar'])
</script>

<style scoped>
.card { background: #fff; border: 1px solid #e0e0e0; border-radius: 6px; padding: 1.5rem; margin-bottom: 1rem; }

.list-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 1rem;
  flex-wrap: wrap;
  gap: .75rem;
}
.card-title { font-size: .88rem; font-weight: 600; margin: 0; }

.toolbar { display: flex; align-items: center; gap: .5rem; flex-wrap: wrap; }
.search {
  padding: .38rem .65rem;
  border: 1px solid #e0e0e0;
  border-radius: 5px;
  font-size: .78rem;
  width: 170px;
  outline: none;
}
.search:focus { border-color: #555; }

.filters { display: flex; gap: .35rem; }
.fbtn {
  padding: .28rem .65rem;
  border-radius: 4px;
  border: 1px solid #e0e0e0;
  background: #fff;
  font-size: .68rem;
  color: #999;
  cursor: pointer;
  transition: all .15s;
  font-family: inherit;
}
.fbtn-on { background: #111; color: #fff; border-color: #111; }

.table { width: 100%; border-collapse: collapse; font-size: .78rem; }
.table th {
  text-align: left;
  font-size: .6rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: .5px;
  color: #aaa;
  padding: .45rem .6rem;
  border-bottom: 1px solid #e0e0e0;
}
.table td {
  padding: .6rem .6rem;
  border-bottom: 1px solid #f0f0f0;
  vertical-align: middle;
}
.table tbody tr:last-child td { border-bottom: none; }
.table tbody tr:hover td      { background: #fafafa; }

.td-mono { font-family: monospace; font-size: .76rem; color: #666; }
.td-sm   { font-size: .74rem; color: #666; max-width: 140px; }
.td-obs  { font-size: .72rem; color: #aaa; max-width: 120px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.td-act  { white-space: nowrap; }

.badge { display: inline-block; padding: .12rem .45rem; border-radius: 4px; font-weight: 700; font-size: .76rem; }
.badge.aprobado    { background: #f0fdf4; color: #16a34a; }
.badge.reprobado   { background: #fef2f2; color: #dc2626; }
.badge.advertencia { background: #fffbeb; color: #d97706; }

.act { background: none; border: none; cursor: pointer; font-size: .78rem; color: #ccc; padding: .18rem .28rem; transition: color .15s; }
.act:hover     { color: #111; }
.act.red:hover { color: #dc2626; }

.empty { text-align: center; padding: 2.5rem; color: #ccc; font-size: .78rem; }
</style>