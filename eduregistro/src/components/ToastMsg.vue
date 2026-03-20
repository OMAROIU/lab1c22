<template>
  <!-- v-if: solo se muestra cuando hay mensaje activo -->
  <div v-if="toast.visible" :class="['toast', toast.tipo]">
    {{ toast.msg }}
  </div>
</template>

<script setup>
defineProps({
  toast: { type: Object, required: true }
})
</script>

<style scoped>
.toast {
  position: fixed;
  bottom: 1.5rem;
  right: 1.5rem;
  padding: .6rem 1rem;
  border-radius: 6px;
  font-size: .78rem;
  font-weight: 500;
  z-index: 999;
  box-shadow: 0 2px 8px rgba(0,0,0,.08);
}
.toast.ok   { background: #f0fdf4; color: #16a34a; border: 1px solid #bbf7d0; }
.toast.warn { background: #fffbeb; color: #d97706; border: 1px solid #fde68a; }
.toast.err  { background: #fef2f2; color: #dc2626; border: 1px solid #fecaca; }
</style>