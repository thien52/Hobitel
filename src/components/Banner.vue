<template>
  <div class="banner-carousel-container">
    <el-carousel :interval="5000" arrow="always" indicator-position="outside" height="600px">
      <el-carousel-item v-for="(item, index) in bannerItems" :key="index">
        <div class="slide-content">
          <img :src="item.src" :alt="item.alt" class="carousel-image">
          <div class="carousel-overlay">
            <div class="banner-text-content">
              <h1 class="animate__animated animate__fadeInUp animate__delay-0.5s">{{ item.title }}</h1>
              <p class="animate__animated animate__fadeInUp animate__delay-1s">{{ item.description }}</p>
              <el-button
                v-if="item.ctaText && item.ctaLink"
                type="primary"
                size="large"
                class="animate__animated animate__fadeInUp animate__delay-1.5s"
                @click="navigateTo(item.ctaLink)"
              >
                {{ item.ctaText }}
              </el-button>
            </div>
          </div>
        </div>
      </el-carousel-item>
    </el-carousel>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router'; // For navigation when the CTA is clicked

// Import Animate.css to add animations to the text
// You need to install Animate.css: npm install animate.css --save
// And import it in main.js/main.ts or here (if only used for this component)
// import 'animate.css'; // If you want to import it specifically for this component

const router = useRouter();

// Data for the banner slides
// Changed to an array of objects to hold more information
const bannerItems = ref([
  {
    src: 'https://hibtel.webhotel.vn/assets/image/banner-1.jpg',
    alt: 'Luxurious Hobitel Hotel',
    title: 'Welcome to Hobitel Hotel',
    description: 'The ideal place for your dream vacation in Da Nang.',
    ctaText: 'Discover Our Rooms',
    ctaLink: '/rooms' // Assuming there is a /rooms page
  },
  {
    src: 'https://hibtel.webhotel.vn/assets/image/banner-2.jpg',
    alt: 'Infinity pool with a sea view',
    title: 'Relax at the Infinity Pool',
    description: 'Immerse yourself in crystal-clear water and enjoy the stunning panoramic sea view.',
    ctaText: 'View Amenities',
    ctaLink: '/services' // Assuming there is a /services page
  },
  {
    src: 'https://hibtel.webhotel.vn/assets/image/banner-3.jpg',
    alt: 'Restaurant with diverse cuisine',
    title: 'Experience World-Class Dining',
    description: 'Enjoy the essence of Asian-European cuisine in a luxurious setting.',
    ctaText: 'Book a Table Now',
    ctaLink: '/restaurant' // Assuming there is a /restaurant page
  },
]);

// Function to navigate when the CTA button is clicked
const navigateTo = (path) => {
  if (path) {
    router.push(path);
  }
};
</script>

<style scoped>
/* Container for the entire carousel, controls width and margin */
.banner-carousel-container {
  width: 100%;
  /* margin-bottom: 3rem; */ /* Optional: add bottom margin if needed */
}

/* Basic styles for El-Carousel */
.el-carousel {
  border-radius: 8px; /* Optional: round the carousel corners */
  overflow: hidden; /* Ensures content doesn't overflow the rounded corners */
}

.el-carousel__item {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f0f2f5; /* Fallback background color */
}

.slide-content {
  position: relative;
  width: 100%;
  height: 100%;
}

.carousel-image {
  width: 100%;
  height: 100%;
  object-fit: cover; /* Ensures the image covers the entire slide */
  object-position: center; /* Center the image */
  filter: brightness(0.6); /* Slightly darken the image to make the text stand out */
  transition: transform 0.8s ease; /* Add a slight zoom effect on hover */
}

/* Slight zoom effect on hovering the slide */
.el-carousel__item:hover .carousel-image {
  transform: scale(1.03);
}

.carousel-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 20px; /* Padding for the content */
  box-sizing: border-box; /* Ensures padding doesn't cause width/height overflow */
}

.banner-text-content {
  color: white;
  z-index: 10;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.7); /* Create a text shadow for readability */
  max-width: 80%; /* Limit the width of the text content */
}

.banner-text-content h1 {
  font-size: 4rem; /* Large font size for the title */
  margin-bottom: 1rem;
  line-height: 1.2;
  font-weight: bold; /* Ensures the title stands out */
}

.banner-text-content p {
  font-size: 1.6rem; /* Font size for the description */
  margin-bottom: 2rem;
  line-height: 1.5;
}

/* Responsive adjustments for various screen sizes */
/* Medium size - Desktops/Small Laptops */
@media (max-width: 1200px) {
  .el-carousel {
    height: 500px !important;
  }
  .banner-text-content h1 {
    font-size: 3.5rem;
  }
  .banner-text-content p {
    font-size: 1.4rem;
  }
}

/* Medium size - Landscape Tablets */
@media (max-width: 992px) {
  .el-carousel {
    height: 450px !important;
  }
  .banner-text-content h1 {
    font-size: 2.8rem;
  }
  .banner-text-content p {
    font-size: 1.2rem;
  }
  .el-button--large {
    padding: 12px 24px;
    font-size: 1rem;
  }
}

/* Small size - Portrait Tablets / Landscape Mobiles */
@media (max-width: 768px) {
  .el-carousel {
    height: 400px !important;
    /* indicator-position: inside; */ /* Optional: move indicators inside to save space */
  }
  .banner-text-content h1 {
    font-size: 2.2rem;
    margin-bottom: 0.8rem;
  }
  .banner-text-content p {
    font-size: 1rem;
    margin-bottom: 1.5rem;
  }
  .el-carousel__arrow {
      display: none; /* Hide navigation arrows on small mobiles */
  }
}

/* Extra small size - Portrait Mobiles */
@media (max-width: 576px) {
  .el-carousel {
    height: 300px !important;
  }
  .banner-text-content h1 {
    font-size: 1.8rem;
  }
  .banner-text-content p {
    font-size: 0.9rem;
  }
  .el-button--large {
    padding: 10px 20px;
    font-size: 0.9rem;
  }
}
</style>