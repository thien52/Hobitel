<template>
  <section class="service-section py-5">
    <div class="container">
      <h2 class="section-title text-center">Explore Our Services</h2>
      <p class="section-subtitle text-center text-muted mb-4">
        Choose the experiences that best suit your needs.
      </p>

      <!-- FILTER CONTROLS -->
      <div class="filter-controls d-flex justify-content-center flex-wrap mb-5">
        <button
          v-for="category in categories"
          :key="category"
          @click="activeCategory = category"
          :class="['filter-btn', { 'active': activeCategory === category }]"
        >
          {{ category }}
        </button>
      </div>

      <!-- SERVICES GRID WITH TRANSITION EFFECT -->
      <TransitionGroup name="service-fade" tag="div" class="services-grid">
        <div 
          v-for="service in filteredServices" 
          :key="service.id" 
          class="service-card"
        >
          <div class="card-image-wrapper">
            <el-image
              :src="service.imageSrc"
              :alt="service.title"
              fit="cover"
              class="card-image"
              lazy
            />
            <span class="category-badge">{{ service.category }}</span>
          </div>
          <div class="card-content">
            <h3 class="card-title">{{ service.title }}</h3>
            <p class="card-description">{{ service.description }}</p>
          </div>
        </div>
      </TransitionGroup>

      <!-- CALL TO ACTION BUTTON -->
      <div class="text-center mt-5">
        <el-button 
          type="danger" 
          size="large" 
          @click="goToServicesPage"
          round
        >
          View & Book All Services
        </el-button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useRouter } from 'vue-router';

// Component's data and logic
const allServices = ref([
  { id: 1, imageSrc: 'https://hibtel.webhotel.vn/assets/image/facilities-1.jpg', title: 'Exquisite Dining Experience', category: 'Dining', description: 'Savor gourmet dishes prepared by our award-winning chefs.' },
  { id: 2, imageSrc: 'https://images.unsplash.com/photo-1544161515-4ab6ce6db874?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1yZWxhdGVkfDE0fHx8ZW58MHx8fHx8&w=1000&q=80', title: 'Spa & Wellness Therapies', category: 'Wellness', description: 'Relax and rejuvenate with a range of spa treatments, massages, and intensive facials.' },
  { id: 3, imageSrc: 'https://i.pinimg.com/736x/57/32/1b/57321b3fca7242ba52da622d3b079b35.jpg', title: 'Modern Conference Rooms', category: 'Events', description: 'Host successful meetings and seminars with modern audiovisual technology and flexible spaces.' },
  { id: 4, imageSrc: 'https://i.pinimg.com/736x/20/42/d5/2042d5e371ed18dd1a7de511b5dbdd81.jpg', title: 'Fitness Center', category: 'Recreation', description: 'Maintain your workout routine with our diverse range of cardio machines and free weights.' },
  { id: 5, imageSrc: 'https://i.pinimg.com/736x/67/4c/8c/674c8c699db95d18e0a19fdd3fff8709.jpg', title: 'Refreshing Swimming Pool', category: 'Recreation', description: 'Immerse yourself in relaxation at our expansive pool, a refreshing escape for the whole family.' },
  { id: 6, imageSrc: 'https://i.pinimg.com/736x/f6/ec/8d/f6ec8d54137cff8900d27a8322984b8d.jpg', title: 'Cooking Class', category: 'Dining', description: 'Join our chef for a cooking class and discover the secrets of local cuisine.' }
]);
const router = useRouter();
const activeCategory = ref('All'); // Changed from 'Tất cả' to 'All'
const HOMEPAGE_SERVICE_LIMIT = 6;
const servicesForHomepage = computed(() => allServices.value.slice(0, HOMEPAGE_SERVICE_LIMIT));
const categories = computed(() => {
    const visibleCategories = servicesForHomepage.value.map(s => s.category);
    return ['All', ...new Set(visibleCategories)]; // Changed from 'Tất cả' to 'All'
});
const filteredServices = computed(() => {
  if (activeCategory.value === 'All') { // Changed from 'Tất cả' to 'All'
    return servicesForHomepage.value;
  }
  return servicesForHomepage.value.filter(service => service.category === activeCategory.value);
});
const goToServicesPage = () => {
  router.push('/services'); 
};
</script>

<style scoped>
/* CSS remains the same, only the comment is translated */
.service-section {
  background: linear-gradient(180deg, #ffffff 0%, #f7f9fc 100%);
}
.container {
    max-width: 1200px;
}
.section-title {
  font-family: 'Playfair Display', serif; font-size: 2.8rem; font-weight: 700; color: #1A3760; margin-bottom: 0.5rem;
}
.section-subtitle {
  max-width: 600px; margin-left: auto; margin-right: auto; font-size: 1.1rem;
}
.filter-controls {
  gap: 15px;
}
.filter-btn {
  background-color: #fff; border: 1px solid #e0e0e0; color: #555; padding: 8px 20px; border-radius: 20px; cursor: pointer; font-weight: 500; transition: all 0.3s ease; font-size: 0.95rem;
}
.filter-btn:hover {
  background-color: #f0f0f0; border-color: #ccc;
}
.filter-btn.active {
  background-color: #1A3760; color: #fff; border-color: #1A3760; box-shadow: 0 4px 10px rgba(26, 55, 96, 0.3); transform: translateY(-2px);
}

.services-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 30px;
  justify-content: start; /* <-- THIS LINE WAS ADDED TO FIX THE LAYOUT */
}

.service-card {
  background-color: #ffffff; border-radius: 12px; box-shadow: 0 4px 25px rgba(0, 0, 0, 0.07); overflow: hidden; transition: transform 0.3s ease, box-shadow 0.3s ease; cursor: pointer; display: flex; flex-direction: column;
}
.service-card:hover {
  transform: translateY(-8px); box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}
.card-image-wrapper {
  position: relative; height: 220px;
}
.card-image {
  width: 100%; height: 100%; display: block;
}
.category-badge {
  position: absolute; top: 15px; left: 15px; background-color: rgba(26, 55, 96, 0.85); color: #fff; padding: 5px 12px; border-radius: 15px; font-size: 0.8rem; font-weight: 600; backdrop-filter: blur(4px);
}
.card-content {
  padding: 25px; flex-grow: 1;
}
.card-title {
    font-family: 'Georgia', serif; font-size: 1.4rem; color: #333; margin-bottom: 10px; font-weight: bold;
}
.card-description {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif; color: #666; line-height: 1.6; font-size: 0.95rem; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; text-overflow: ellipsis; margin-bottom: 0; font-weight: 400;
}
.service-fade-enter-active,
.service-fade-leave-active {
  transition: all 0.5s cubic-bezier(0.55, 0, 0.1, 1);
}
.service-fade-enter-from,
.service-fade-leave-to {
  opacity: 0; transform: scale(0.9);
}
.el-button--danger {
  background-color: #C09153 !important; border-color: #C09153 !important; padding: 14px 35px; font-weight: bold;
}
.el-button--danger:hover {
  background-color: #b38246 !important; border-color: #b38246 !important;
}
</style>