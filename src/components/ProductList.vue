<template>
  <div class="product-list-section">
    <h2 class="list-title">{{ listTitle }}</h2>
    <div v-if="products && products.length > 0" class="products-grid">
      <ProductCard
        v-for="product in products"
        :key="product.id"
        :product="product"
        @view-details="handleViewDetails"
      />
    </div>
    <div v-else class="no-products">
      <p>😕 No se encontraron mangas que coincidan con tu búsqueda.</p>
      <p v-if="isFiltered">Intenta con otros filtros o categorías.</p>
    </div>
  </div>
</template>

<script setup>
import ProductCard from './ProductCard.vue';

defineProps({
  products: {
    type: Array,
    required: true
  },
  listTitle: {
    type: String,
    default: 'Nuestros Mangas Destacados'
  },
  isFiltered: { 
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['open-product-modal']);

const handleViewDetails = (product) => {
  emit('open-product-modal', product);
};
</script>

<style scoped>
.product-list-section {
  padding: 20px 10px;
  max-width: 1200px;
  margin: 0 auto; 
}
.list-title {
  font-size: 1.8rem;
  font-weight: 600;
  color: #333;
  margin-bottom: 25px;
  text-align: center;
  padding-bottom: 10px;
  border-bottom: 2px solid #e85a4f;
  display: inline-block; 
}
.products-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); 
  gap: 25px;
  justify-content: center;
}
.no-products {
  text-align: center;
  padding: 40px 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  margin-top: 20px;
}
.no-products p {
  font-size: 1.2rem;
  color: #555;
  margin-bottom: 10px;
}
</style>