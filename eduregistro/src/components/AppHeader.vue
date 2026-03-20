<template>
  <header class="header">
    <div>
      <h1 class="title">EduRegistro</h1>
      <p class="subtitle">Registro de estudiantes y notas · UGB 2025-01</p>
    </div>
    <span class="counter">
      {{ total }} registro{{ total !== 1 ? 's' : '' }}
    </span>
  </header>
</template>

<script setup>
defineProps({
  total: { type: Number, required: true }
})
</script>

<style scoped>
.header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid #e0e0e0;
  margin-bottom: 1.5rem;
}
.title    { font-size: 1.4rem; font-weight: 700; letter-spacing: -.4px; }
.subtitle { font-size: .75rem; color: #888; margin-top: .1rem; }
.counter  { font-size: .72rem; color: #bbb; padding-top: .3rem; }

@media (max-width: 560px) {
  .header { flex-direction: column; gap: .4rem; }
}
</style>