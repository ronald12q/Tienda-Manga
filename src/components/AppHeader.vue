<template>
  <header class="app-header">
    <div class="store-info">
      <h1 class="store-name">Manga Soul</h1>
    </div>
    <div class="cart-summary" @click="$emit('open-cart-modal')">
      <span class="cart-icon">🛒</span>
      <span class="cart-text">Carrito</span>
      <span class="cart-total">(${{ totalCarrito.toFixed(2) }})</span>
    </div>
  </header>
</template>

<script setup>
defineProps({
  totalCarrito: {
    type: Number,
    required: true
  }
});

defineEmits(['open-cart-modal']);
</script>

<style scoped>
/* Tus estilos existentes */
.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem 1.5rem;
  background-color: #2c3e50;
  color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.store-info {
  display: flex;
  align-items: center;
}
.logo {
  height: 45px;
  margin-right: 10px;
}
.store-name {
  font-size: 1.8rem;
  font-family: 'Bangers', cursive;
  margin: 0;
  font-weight: 400;
}
.cart-summary {
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 8px 12px;
  border-radius: 5px;
  transition: background-color 0.2s ease-in-out;
}
.cart-summary:hover {
  background-color: #34495e;
}
.cart-icon {
  font-size: 1.6rem;
  margin-right: 8px;
}
.cart-text {
  font-size: 1rem;
  margin-right: 5px;
}
.cart-total {
  font-size: 1rem;
  font-weight: bold;
}
</style>