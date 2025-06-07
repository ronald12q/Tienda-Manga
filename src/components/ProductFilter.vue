<template>
  <div class="product-filters-wrapper">
    <div class="product-filters">
      <div class="filter-group">
        <label for="category-select" class="filter-label">Categoría:</label>
        <select id="category-select" :value="modelValueCategory" @change="emitCategoryChange($event.target.value)" class="filter-select">
          <option value="Todas">Todas las Categorías</option>
          <option v-for="category in categories" :key="category" :value="category">
            {{ category }}
          </option>
        </select>
      </div>
      <div class="filter-group">
        <label for="search-input" class="filter-label">Buscar Manga:</label>
        <input
          type="text"
          id="search-input"
          :value="modelValueSearchTerm"
          @input="emitSearchTermChange($event.target.value)"
          placeholder="Ej: Attack on Titan, Berserk..."
          class="filter-input"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  categories: {
    type: Array,
    required: true
  },
  modelValueCategory: String,
  modelValueSearchTerm: String
});

const emit = defineEmits(['update:modelValueCategory', 'update:modelValueSearchTerm']);

const emitCategoryChange = (value) => {
  emit('update:modelValueCategory', value);
};

const emitSearchTermChange = (value) => {
  emit('update:modelValueSearchTerm', value);
};
</script>

<style scoped>
.product-filters-wrapper {
  padding: 15px 10px;
  margin-bottom: 25px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
}
.product-filters {
  display: flex;
  flex-wrap: wrap; 
  gap: 20px;
  align-items: flex-end; 
  justify-content: center;
}
.filter-group {
  display: flex;
  flex-direction: column;
  min-width: 250px; 
  flex-grow: 1; 
}
.filter-label {
  font-weight: 600;
  margin-bottom: 8px;
  font-size: 0.95rem;
  color: #333;
}
.filter-select,
.filter-input {
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 1rem;
  width: 100%; 
  box-sizing: border-box; 
}
.filter-select:focus,
.filter-input:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 0 0.2rem rgba(0,123,255,.25);
}
</style>