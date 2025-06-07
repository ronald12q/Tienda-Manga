<template>
  <div class="modal-overlay" @click.self="closeModal">
    <div class="modal-content cart-modal-content">
      <button @click="closeModal" class="close-btn-modal">&times;</button>
      <h2 class="modal-title">Tu Carrito de Compras</h2>

      <div v-if="paymentSuccessMessage" class="payment-status success">
        <p>{{ paymentSuccessMessage }}</p>
        <button @click="closeModalAndReset" class="action-btn ok-btn">Entendido</button>
      </div>

      <div v-else-if="cartItems.length === 0" class="empty-cart">
        <p>🛍️ Tu carrito está vacío.</p>
        <p>¡Añade algunos mangas!</p>
      </div>

      <div v-else class="cart-details">
        <ul class="cart-item-list">
          <li v-for="item in cartItems" :key="item.id" class="cart-item">
            <img :src="getImageUrl(item.imagen)" :alt="item.nombre" class="cart-item-image"/>
            <div class="cart-item-info">
              <span class="item-name">{{ item.nombre }}</span>
              <div class="item-quantity-controls">
                <button @click="$emit('update-quantity', item.id, item.cantidad - 1)" class="quantity-btn">-</button>
                <span class="item-quantity">{{ item.cantidad }}</span>
                <button @click="$emit('update-quantity', item.id, item.cantidad + 1)" class="quantity-btn">+</button>
              </div>
              <span class="item-price-unit">${{ item.precio.toFixed(2) }} c/u</span>
            </div>
            <div class="cart-item-subtotal-actions">
              <span class="item-subtotal">${{ (item.precio * item.cantidad).toFixed(2) }}</span>
              <button @click="$emit('remove-from-cart', item.id)" class="remove-item-btn">🗑️</button>
            </div>
          </li>
        </ul>
        <div class="cart-summary-section">
          <div class="cart-total">
            <strong>Total a Pagar:</strong>
            <span class="total-amount">${{ total.toFixed(2) }}</span>
          </div>
          <button @click="$emit('process-payment')" class="action-btn pay-btn">Proceder al Pago</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  cartItems: { type: Array, required: true },
  total: { type: Number, required: true },
  paymentSuccessMessage: String
});

const emit = defineEmits(['close-cart-modal', 'process-payment', 'remove-from-cart', 'update-quantity', 'reset-payment-message']);

const getImageUrl = (imageName) => {
  if (!imageName) return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  try {
    return new URL(`../assets/images/products/${imageName}`, import.meta.url).href;
  } catch (e) {
    return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  }
};

const closeModal = () => {
  if (props.paymentSuccessMessage) {
    emit('reset-payment-message'); // Limpia el mensaje si solo se cierra se toca fuera del modal
  }
  emit('close-cart-modal');
};

const closeModalAndReset = () => {
    emit('reset-payment-message');
    emit('close-cart-modal');
}
</script>

<style scoped>

.modal-overlay {
  position: fixed; top: 0; left: 0; width: 100%; height: 100%;
  background-color: rgba(0, 0, 0, 0.65); display: flex;
  justify-content: center; align-items: center; z-index: 1000;
  padding: 20px; box-sizing: border-box;
}
.modal-content {
  background-color: white; padding: 20px 25px; border-radius: 10px;
  width: 100%; max-width: 650px; max-height: 90vh; position: relative;
  box-shadow: 0 5px 20px rgba(0,0,0,0.3); display: flex; flex-direction: column;
}
.cart-modal-content { 
  min-height: 300px; 
}
.close-btn-modal { 
  position: absolute; top: 12px; right: 15px; background: none; border: none;
  font-size: 2.2rem; line-height: 1; cursor: pointer; color: #888; padding: 5px;
}
.close-btn-modal:hover { color: #333; }

.modal-title {
  font-size: 1.6rem; color: #333; font-weight: 600;
  margin-top: 0; margin-bottom: 20px; text-align: center;
  padding-bottom: 10px; border-bottom: 1px solid #eee;
}

.payment-status {
  text-align: center; padding: 20px; border-radius: 8px; margin: 20px 0;
}
.payment-status.success {
  background-color: #d4edda; color: #155724; border: 1px solid #c3e6cb;
}
.payment-status p { margin-bottom: 15px; font-size: 1.1rem; }
.ok-btn { background-color: #28a745; color: white; }
.ok-btn:hover { background-color: #218838; }


.empty-cart {
  text-align: center; padding: 30px 0; flex-grow: 1;
  display: flex; flex-direction: column; justify-content: center; align-items: center;
}
.empty-cart p { font-size: 1.2rem; color: #666; margin: 5px 0; }
.empty-cart p:first-child { font-size: 2rem; margin-bottom: 15px; } /* Icono grande */

.cart-details {
  display: flex;
  flex-direction: column;
  flex-grow: 1; 
  overflow: hidden; 
}

.cart-item-list {
  list-style: none; padding: 0; margin: 0 0 20px 0;
  overflow-y: auto; 
  max-height: 400px; 
  border: 1px solid #f0f0f0;
  border-radius: 6px;
}
.cart-item {
  display: flex; align-items: center; padding: 12px 15px;
  border-bottom: 1px solid #f0f0f0;
}
.cart-item:last-child { border-bottom: none; }

.cart-item-image {
  width: 70px; height: 90px; object-fit: contain;
  margin-right: 15px; border-radius: 4px; border: 1px solid #eee;
}
.cart-item-info {
  flex-grow: 1; display: flex; flex-direction: column; gap: 5px;
}
.item-name { font-weight: 600; color: #444; font-size: 1rem; }
.item-price-unit { font-size: 0.85rem; color: #777; }

.item-quantity-controls { display: flex; align-items: center; gap: 8px; margin: 4px 0; }
.quantity-btn {
  background-color: #f0f0f0; border: 1px solid #ddd; color: #555;
  border-radius: 4px; width: 28px; height: 28px; font-size: 1rem;
  cursor: pointer; display: flex; align-items: center; justify-content: center;
}
.quantity-btn:hover { background-color: #e0e0e0; }
.item-quantity { font-weight: 500; min-width: 20px; text-align: center; }


.cart-item-subtotal-actions {
  display: flex; flex-direction: column; align-items: flex-end; gap: 8px;
  margin-left: 15px;
}
.item-subtotal { font-weight: 600; color: #333; font-size: 1rem; }
.remove-item-btn {
  background: none; border: none; color: #e74c3c;
  font-size: 1.3rem; cursor: pointer; padding: 0;
}
.remove-item-btn:hover { color: #c0392b; }

.cart-summary-section {
  margin-top: auto; 
  padding-top: 20px; border-top: 2px solid #333;
}
.cart-total {
  display: flex; justify-content: space-between; align-items: center;
  font-size: 1.3rem; margin-bottom: 20px;
}
.cart-total strong { color: #333; }
.total-amount { font-weight: bold; color: #e85a4f; }

.action-btn { 
  padding: 10px 20px; border: none; border-radius: 6px; cursor: pointer;
  font-weight: 600; font-size: 1rem; transition: background-color 0.2s;
}
.pay-btn {
  background-color: #007bff; color: white; width: 100%;
  padding: 12px; font-size: 1.1rem;
}
.pay-btn:hover { background-color: #0056b3; }
</style>