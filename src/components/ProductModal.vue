<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal-content product-modal-content">
      <button @click="$emit('close')" class="close-btn-modal">&times;</button>
      <div class="modal-body">
        <div class="product-image-wrapper">
          <img :src="getImageUrl(product.imagen)" :alt="product.nombre" class="modal-product-image"/>
        </div>
        <div class="product-details-wrapper">
          <h2 class="modal-title">{{ product.nombre }}</h2>
          <p class="modal-category"><strong>Categoría:</strong> {{ product.categoria }}</p>
          <p class="modal-price"><strong>Precio:</strong> ${{ product.precio.toFixed(2) }}</p>
          <p class="modal-stock"><strong>Disponibles:</strong> {{ product.stock > 0 ? product.stock : 'Agotado' }}</p>
          <h4 class="modal-description-title">Descripción:</h4>
          <p class="modal-description">{{ product.descripcion }}</p>
          <div class="modal-actions">
            <button
              @click="handleAddToCart"
              class="action-btn add-to-cart-btn"
              :disabled="product.stock === 0"
            >
              {{ product.stock > 0 ? 'Agregar al Carrito' : 'Agotado' }}
            </button>
            <button @click="$emit('close')" class="action-btn cancel-btn-modal">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  product: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['close', 'add-to-cart']);

const getImageUrl = (imageName) => {
  if (!imageName) return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  try {
    return new URL(`../assets/images/products/${imageName}`, import.meta.url).href;
  } catch (e) {
    return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  }
};

const handleAddToCart = () => {
  if (props.product.stock > 0) {
    emit('add-to-cart', props.product);
  }
};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.65);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  padding: 20px;
  box-sizing: border-box;
}
.modal-content {
  background-color: white;
  padding: 25px 30px;
  border-radius: 10px;
  width: 100%;
  max-width: 750px; 
  max-height: 90vh; 
  position: relative;
  box-shadow: 0 5px 20px rgba(0,0,0,0.3);
  overflow-y: auto; 
}
.close-btn-modal {
  position: absolute;
  top: 12px;
  right: 15px;
  background: none;
  border: none;
  font-size: 2.2rem;
  line-height: 1;
  cursor: pointer;
  color: #888;
  padding: 5px;
}
.close-btn-modal:hover {
  color: #333;
}

.modal-body {
  display: flex;
  gap: 25px;
  flex-wrap: wrap; 
}

.product-image-wrapper {
  flex: 1 1 250px; 
  max-width: 300px; 
  margin: 0 auto; 
}
.modal-product-image {
  width: 100%;
  height: auto;
  max-height: 400px;
  object-fit: contain;
  border-radius: 8px;
  border: 1px solid #eee;
}

.product-details-wrapper {
  flex: 2 1 300px; 
  display: flex;
  flex-direction: column;
}

.modal-title {
  margin-top: 0;
  margin-bottom: 10px;
  font-size: 1.8rem;
  color: #333;
  font-weight: 600;
}
.modal-category, .modal-price, .modal-stock {
  font-size: 1rem;
  color: #555;
  margin-bottom: 8px;
}
.modal-price strong {
  color: #e85a4f;
  font-size: 1.2rem;
}
.modal-stock strong {
  color: #333;
}
.modal-description-title {
  font-size: 1.1rem;
  color: #444;
  margin-top: 15px;
  margin-bottom: 5px;
  font-weight: 600;
}
.modal-description {
  font-size: 0.95rem;
  color: #666;
  line-height: 1.6;
  margin-bottom: 20px;
  flex-grow: 1; 
  overflow-y: auto; 
  max-height: 150px; 
}

.modal-actions {
  margin-top: auto; 
  padding-top: 15px; 
  border-top: 1px solid #eee; 
  display: flex;
  gap: 15px;
  justify-content: flex-end;
}
.action-btn {
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  font-size: 1rem;
  transition: background-color 0.2s, opacity 0.2s;
}
.add-to-cart-btn {
  background-color: #28a745;
  color: white;
}
.add-to-cart-btn:hover {
  background-color: #218838;
}
.add-to-cart-btn:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
  opacity: 0.7;
}
.cancel-btn-modal {
  background-color: #6c757d;
  color: white;
}
.cancel-btn-modal:hover {
  background-color: #5a6268;
}


@media (max-width: 600px) {
  .modal-body {
    flex-direction: column;
  }
  .product-image-wrapper {
    max-width: 100%; 
    margin-bottom: 15px;
  }
  .modal-product-image {
    max-height: 250px; 
  }
  .modal-actions {
    flex-direction: column; 
  }
  .action-btn {
    width: 100%;
  }
}
</style>