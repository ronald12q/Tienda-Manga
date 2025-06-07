<template>
  <div id="app-container">
    <AppHeader
      :totalCarrito="cartTotal"
      @open-cart-modal="isCartModalOpen = true"
    />
  
<!-- configuramos el header  -->
    <ImageSlider />
    <!-- no hacemos nada con el slider solo traerlo aqui  -->

    <main class="main-content">
      <ProductFilter
        :categories="categoriesForFilter"
        v-model:modelValueCategory="selectedCategory"
        v-model:modelValueSearchTerm="searchTerm"
      />
      <!-- configuracion del input de busqueda por filtro de categoria   -->

      <ProductList
        :products="filteredProducts"
        :listTitle="currentCategoryTitle"
        :isFiltered="selectedCategory !== 'Todas' || searchTerm !== ''"
        @open-product-modal="showProductModal"
      />
    </main>

    <ProductModal
      v-if="selectedProduct"
      :product="selectedProduct"
      @close="selectedProduct = null"
      @add-to-cart="addToCart"
    />

    <CartModal
      v-if="isCartModalOpen"
      :cartItems="cart"
      :total="cartTotal"
      :paymentSuccessMessage="paymentMessage"
      @close-cart-modal="isCartModalOpen = false"
      @reset-payment-message="paymentMessage = ''"
      @process-payment="handlePayment"
      @remove-from-cart="removeFromCart"
      @update-quantity="updateCartItemQuantity"
    />

    <footer class="app-footer">
      <p>&copy; {{ new Date().getFullYear() }} Manga Soul. Todos los derechos reservados.</p>
      <p>Tu tienda online de mangas favorita</p>
      <p>Gracias.</p>
    </footer>
    <!-- solo el footer con lo tipico   -->
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import Swal from 'sweetalert2';

// Componentes
import AppHeader from './components/AppHeader.vue';
import ImageSlider from './components/ImageSlider.vue';
import ProductFilter from './components/ProductFilter.vue';
import ProductList from './components/ProductList.vue';
import ProductModal from './components/ProductModal.vue';
import CartModal from './components/CartModal.vue';

// Estado reactivo
const allProducts = ref([]);
const cart = ref([]); 
const selectedProduct = ref(null); 
const isCartModalOpen = ref(false);
const paymentMessage = ref('');

// Estados para filtros
const selectedCategory = ref('Todas'); 
const searchTerm = ref('');         

// --- CARGA DE PRODUCTOS ---
onMounted(async () => {
  try {
    const response = await fetch('/products.json'); 
                                                             
    if (!response.ok) {
      throw new Error(`Error al cargar productos: ${response.statusText}`);
    }
    allProducts.value = await response.json();
  } catch (error) {
    console.error("No se pudieron cargar los productos:", error);
    
  }
});


const categoriesForFilter = computed(() => {
  const uniqueCategories = new Set(allProducts.value.map(p => p.categoria));
  return Array.from(uniqueCategories).sort();
});

const filteredProducts = computed(() => {
  let products = allProducts.value;

 
  if (selectedCategory.value && selectedCategory.value !== 'Todas') {
    products = products.filter(p => p.categoria === selectedCategory.value);
  }

 
  if (searchTerm.value) {
    const lowerSearchTerm = searchTerm.value.toLowerCase();
    products = products.filter(p => p.nombre.toLowerCase().includes(lowerSearchTerm));
  }
  return products;
});

const currentCategoryTitle = computed(() => {
  if (selectedCategory.value && selectedCategory.value !== 'Todas') {
    let title = `Mangas de: ${selectedCategory.value}`;
    if (searchTerm.value) {
      title += ` (buscando "${searchTerm.value}")`;
    }
    return title;
  }
  if (searchTerm.value) {
    return `Resultados de búsqueda para: "${searchTerm.value}"`;
  }
  return 'Todos Nuestros Mangas';
});

const cartTotal = computed(() => {
  return cart.value.reduce((sum, item) => sum + (item.precio * item.cantidad), 0);
});


const showProductModal = (product) => {
  selectedProduct.value = product;
};

const addToCart = (productToAdd) => {
  const existingItem = cart.value.find(item => item.id === productToAdd.id);
  if (existingItem) {
    if (existingItem.cantidad < productToAdd.stock) { // Verificar stock
      existingItem.cantidad++;
     
    } else {
      Swal.fire({
        title: '¡Error!',
        text: `No hay suficiente stock. Stock disponible: ${productToAdd.stock}`,
        icon: 'error', 
        timer: 2000,
        showConfirmButton: false
      })
    }
  } else {
    if (productToAdd.stock > 0) {
      cart.value.push({ ...productToAdd, cantidad: 1 });
        Swal.fire({
        title: '¡Exito!',
        text: `Producto agregado correctamente al carrito`,
        icon: 'success', 
        timer: 2000,
        showConfirmButton: false
      })
      
      
    } else {
       alert(`"${productToAdd.nombre}" está agotado.`);
    }
  }
  selectedProduct.value = null; // Cierra el modal de producto si estaba abierto
};

const removeFromCart = (productId) => {
  cart.value = cart.value.filter(item => item.id !== productId);
};

const updateCartItemQuantity = (productId, newQuantity) => {
  const item = cart.value.find(p => p.id === productId);
  if (item) {
    if (newQuantity <= 0) {
      removeFromCart(productId);
    } else if (newQuantity <= item.stock) { // Respetar el stock
      item.cantidad = newQuantity;
    } else {
      Swal.fire({
        title: '¡Error!',
        text: `No hay suficiente stock. Stock disponible: ${item.stock}`,
        icon: 'error', // Cambiado de 'failed' a 'error'
        timer: 2000,
        showConfirmButton: false
      })
      item.cantidad = item.stock; // Ajustar al máximo stock
    }
  }
};

const handlePayment = () => {
  if (cart.value.length === 0) {
    paymentMessage.value = 'Tu carrito está vacío. Añade productos antes de pagar.';
    setTimeout(() => { paymentMessage.value = ''; }, 3000);
    return;
  }

  // Simular pago las actualizaciones en el stock no funciona
  paymentMessage.value = '¡Pago realizado con éxito! Gracias por tu compra en Manga Soul.';
  
  setTimeout(() => {
    cart.value = [];
    
  },1500); 

}

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Bangers&display=swap'); /* Para el logo */

/* Estilos Globales */
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --success-color: #28a745;
  --danger-color: #dc3545;
  --warning-color: #ffc107;
  --info-color: #17a2b8;
  --light-color: #f8f9fa;
  --dark-color: #343a40;
  --text-color: #212529;
  --background-color: #f0f2f5;
  --font-family-sans-serif: 'Roboto', sans-serif;
}

body {
  margin: 0;
  font-family: var(--font-family-sans-serif);
  background-color: var(--background-color);
  color: var(--text-color);
  line-height: 1.6;
  font-size: 16px;
}

#app-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.main-content {
  flex-grow: 1;
  padding: 15px; 
  max-width: 1300px; 
  margin: 0 auto; 
  width: 100%;
  box-sizing: border-box;
}


::-webkit-scrollbar {
  width: 8px;
  height: 8px;
  border-radius: 8px;
  
}
::-webkit-scrollbar-track {
  background: #ffffff;
  border-radius: 10px;
}
::-webkit-scrollbar-thumb {
  background:#4000ff;
  border-radius: 10px;
}
::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}


.app-footer {
  background-color: #2c3e50; 
  color: white;
  text-align: center;
  padding: 20px 15px;
  margin-top: 30px; 
  font-size: 0.9rem;
}
.app-footer p {
  margin: 5px 0;
}
</style>