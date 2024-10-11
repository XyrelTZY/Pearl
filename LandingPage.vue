<template>
    <div class="relative h-[51.5rem] w-full overflow-hidden rounded-lg"> 
      <Carousel :value="images" :circular="true" :autoplayInterval="3000" @change="onImageChange">
        <template #item="slotProps">
            <img :src="slotProps.data.src" :alt="slotProps.data.alt" class="w-fit h-fit mx-auto" />
        </template>
      </Carousel>
      <div class="absolute inset-0 bg-gradient-to-b from-transparent to-black flex flex-col items-center justify-center text-white mt-24">
        <div class="container mx-auto px-4 text-center">
          <h1 class="text-5xl font-bold mb-4 text-white dark:text-white">Welcome, Admin</h1>
          <Button label="Dashboard" class="p-button-raised p-button-lg" />
        </div>
      </div>
    </div>
</template>
<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import img from '@/assets/DummyPicture/SchoolLogo.png'
  
  const images = ref([
    { src: img, alt: 'Image 1' },
    { src: img, alt: 'Image 2' },
    { src: img, alt: 'Image 3' },
    // ... more images
  ]);
  
  let intervalId;
  
  const startCarousel = () => {
    intervalId = setInterval(() => {
      // Logic to switch to the next image
    }, 3000); 
  };
  
  const stopCarousel = () => {
    clearInterval(intervalId);
  };
  
  onMounted(() => {
    startCarousel();
  });
  
  onUnmounted(() => {
    stopCarousel();
  });
  
  const onImageChange = (event) => {
    // Handle image change event if needed
  };
  </script>