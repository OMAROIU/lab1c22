<template>
  <div class="summary">
    <div class="card">
      <span class="lbl">Total</span>
      <span class="val">{{ total }}</span>
    </div>
    <div class="card">
      <span class="lbl">Promedio</span>
      <!-- v-bind (:class): colorea el valor según la nota -->
      <span :class="['val', claseNota(promedio)]">{{ promedio.toFixed(1) }}</span>
    </div>
    <div class="card">
      <span class="lbl">Aprobados</span>
      <span class="val pass">{{ aprobados }}</span>
    </div>
    <div class="card">
      <span class="lbl">Reprobados</span>
      <span class="val fail">{{ reprobados }}</span>
    </div>
  </div>
</template>

<script setup>
defineProps({
  total:      { type: Number,   required: true },
  promedio:   { type: Number,   required: true },
  aprobados:  { type: Number,   required: true },
  reprobados: { type: Number,   required: true },
  claseNota:  { type: Function, required: true },
})
</script>

<style scoped>
.summary {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: .75rem;
  margin-bottom: 1.25rem;
}
.card {
  background: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  padding: .85rem 1rem;
  display: flex;
  flex-direction: column;
  gap: .2rem;
}
.lbl { font-size: .62rem; text-transform: uppercase; letter-spacing: 1px; color: #aaa; }
.val { font-size: 1.4rem; font-weight: 700; color: #111; }
.val.pass        { color: #16a34a; }
.val.fail        { color: #dc2626; }
.val.aprobado    { color: #16a34a; }
.val.reprobado   { color: #dc2626; }
.val.advertencia { color: #d97706; }

@media (max-width: 560px) {
  .summary { grid-template-columns: 1fr 1fr; }
}
</style>