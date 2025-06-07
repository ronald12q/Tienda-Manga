<template>
  <div class="product-card">
    <div class="product-image-container">
      <img :src="getImageUrl(product.imagen)" :alt="product.nombre" class="product-image"/>
    </div>
    <div class="product-info">
      <h3 class="product-name" :title="product.nombre">{{ product.nombre }}</h3>
      <p class="product-category">{{ product.categoria }}</p>
      <p class="product-price">${{ product.precio.toFixed(2) }}</p>
    </div>
    <button @click="$emit('view-details', product)" class="details-btn">Ver Detalles</button>
  </div>
</template>

<script setup>
defineProps({
  product: {
    type: Object,
    required: true
  }
});

defineEmits(['view-details']);

const getImageUrl = (imageName) => {
  if (!imageName) return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  try {
    return new URL(`../assets/images/products/${imageName}`, import.meta.url).href;
  } catch (e) {
    console.warn("Error al cargar imagen del producto:", imageName, e);
    return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  }
};
</script>

<style scoped>
.product-card {
  border: 1px solid #e0e0e0;
  border-radius: 10px;
  background-color: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  display: flex;
  flex-direction: column;
  overflow: hidden; 
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
  width: 250px; 
}
.product-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 16px rgba(0,0,0,0.12);
}
.product-image-container {
  width: 100%;
  height: 280px; 
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background-color: #f8f8f8; 
}
.product-image {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}
.product-info {
  padding: 15px;
  text-align: center;
  flex-grow: 1; 
  display: flex;
  flex-direction: column;
  justify-content: space-between; 
}
.product-name {
  font-size: 1.15rem;
  font-weight: 600;
  color: #333;
  margin: 0 0 8px 0;
  line-height: 1.3;
 
  display: -webkit-box;
  
  overflow: hidden;
  text-overflow: ellipsis;
  min-height: 2.6em; 
}
.product-category {
  font-size: 0.85rem;
  color: #777;
  margin: 0 0 10px 0;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}
.product-price {
  font-size: 1.3rem;
  font-weight: bold;
  color: #e85a4f; 
  margin: 0 0 15px 0;
}
.details-btn {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 12px 15px;
  border-bottom-left-radius: 10px; 
  border-bottom-right-radius: 10px;
  cursor: pointer;
  font-weight: 600;
  font-size: 1rem;
  width: 100%; 
  transition: background-color 0.2s;
  margin-top: auto; 
}
.details-btn:hover {
  background-color: #0056b3;
}
</style>