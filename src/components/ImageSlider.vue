<template>
  <div class="slider-container" v-if="images.length > 0">
    <div class="slider" :style="{ transform: `translateX(-${currentIndex * 100}%)` }">
      <div v-for="(image, index) in images" :key="index" class="slide">
        <img :src="getImageUrl(image.src)" :alt="image.alt" />
      </div>
    </div>
    <button @click="prevSlide" class="nav-btn prev-btn">‹</button>
    <button @click="nextSlide" class="nav-btn next-btn">›</button>
    <div class="dots">
      <span
        v-for="(images, index) in images"
        :key="`dot-${index}`"
        class="dot"
        :class="{ active: currentIndex === index }"
        @click="goToSlide(index)"
      ></span>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';


const images = ref([
  { src: 'num1.png', alt: 'manga' },
  { src: 'num2.png', alt: 'manga' },
  { src: 'Num4.png', alt: 'manga' },
]);

const currentIndex = ref(0);
let autoSlideInterval = null;

const getImageUrl = (name) => {
  // Esta función ayuda a Vite a encontrar las imágenes en tiempo de compilación
  try {
    return new URL(`../assets/images/slider/${name}`, import.meta.url).href;
  } catch(e) {
    console.warn(`Imagen del slider no encontrada: ${name}`);
    return new URL(`../assets/images/placeholder.png`, import.meta.url).href;
  }
};

const nextSlide = () => {
  currentIndex.value = (currentIndex.value + 1) % images.value.length;
};

const prevSlide = () => {
  currentIndex.value = (currentIndex.value - 1 + images.value.length) % images.value.length;
};

const goToSlide = (index) => {
  currentIndex.value = index;
};

const startAutoSlide = () => {
  stopAutoSlide(); // Evita múltiples intervalos
  autoSlideInterval = setInterval(nextSlide, 4000); // Cambia cada 5 segundos
};

const stopAutoSlide = () => {
  clearInterval(autoSlideInterval);
};

onMounted(() => {
  if (images.value.length > 1) {
    startAutoSlide();
  }
});

onUnmounted(() => {
  stopAutoSlide();
});
</script>

<style scoped>
.slider-container {
  position: relative;
  width: 100%;
  max-width: 1150px; /* Ancho máximo del slider */
  margin: 20px auto;
  overflow: hidden;
  border-radius: 10px;
  height: 600px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
}
.slider {
  display: flex;
  transition: transform 0.7s cubic-bezier(0.25, 0.8, 0.25, 1); 
}
.slide {
  min-width: 100%;
  box-sizing: border-box;
}
.slide img {
  width: 100%;
  height: auto; 
  object-fit: cover; 
  display: block;
}
.nav-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  border: none;
  padding: 12px;
  cursor: pointer;
  font-size: 2rem;
  z-index: 10;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s;
}
.nav-btn:hover {
  background-color: rgba(0, 0, 0, 0.8);
}
.prev-btn { left: 15px; }
.next-btn { right: 15px; }

.dots {
  position: absolute;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 8px;
  z-index: 10;
}
.dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: background-color 0.3s;
}
.dot:hover {
  background-color: rgba(255, 255, 255, 0.8);
}
.dot.active {
  background-color: white;
}
</style>