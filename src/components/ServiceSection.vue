<template>
  <section class="service-section py-5">
    <div class="container">
      <h2 class="section-title text-center">Explore Our Services</h2>
      <p class="section-subtitle text-center text-muted mb-4">
        Choose the experiences that best suit your needs.
      </p>

      <TransitionGroup name="service-fade" tag="div" class="services-grid">
        <div
          v-for="service in servicesForHomepage"
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
      <div class="text-center mt-5" v-if="!isLoading">
        <el-button type="danger" size="large" @click="goToServicesPage" round>
          View & Book All Services
        </el-button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import { useRouter } from "vue-router";
import axios from "@/axios";

// Component's data and logic
const allServices = ref([]);
const router = useRouter();
const HOMEPAGE_SERVICE_LIMIT = 3;
const servicesForHomepage = computed(() =>
  allServices.value.slice(0, HOMEPAGE_SERVICE_LIMIT)
);

const isLoading = ref(true);
const goToServicesPage = () => {
  router.push("/services");
};
onMounted(async () => {
  try {
    const res = await axios.get(
      "http://localhost:3000/services?featured=true&_limit=3"
    );
    allServices.value = res.data;
  } catch (err) {
    console.error("Lỗi khi tải services:", err);
  } finally {
    isLoading.value = false;
  }
});
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
  font-family: "Playfair Display", serif;
  font-size: 2.8rem;
  font-weight: 700;
  color: #1a3760;
  margin-bottom: 0.5rem;
}
.section-subtitle {
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  font-size: 1.1rem;
}
.filter-controls {
  gap: 15px;
}
.filter-btn {
  background-color: #fff;
  border: 1px solid #e0e0e0;
  color: #555;
  padding: 8px 20px;
  border-radius: 20px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.3s ease;
  font-size: 0.95rem;
}
.filter-btn:hover {
  background-color: #f0f0f0;
  border-color: #ccc;
}
.filter-btn.active {
  background-color: #1a3760;
  color: #fff;
  border-color: #1a3760;
  box-shadow: 0 4px 10px rgba(26, 55, 96, 0.3);
  transform: translateY(-2px);
}

.services-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 30px;
  justify-content: start; /* <-- THIS LINE WAS ADDED TO FIX THE LAYOUT */
}

.service-card {
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 25px rgba(0, 0, 0, 0.07);
  overflow: hidden;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  cursor: pointer;
  display: flex;
  flex-direction: column;
}
.service-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}
.card-image-wrapper {
  position: relative;
  height: 220px;
}
.card-image {
  width: 100%;
  height: 100%;
  display: block;
}
.category-badge {
  position: absolute;
  top: 15px;
  left: 15px;
  background-color: rgba(26, 55, 96, 0.85);
  color: #fff;
  padding: 5px 12px;
  border-radius: 15px;
  font-size: 0.8rem;
  font-weight: 600;
  backdrop-filter: blur(4px);
}
.card-content {
  padding: 25px;
  flex-grow: 1;
}
.card-title {
  font-family: "Georgia", serif;
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 10px;
  font-weight: bold;
}
.card-description {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  color: #666;
  line-height: 1.6;
  font-size: 0.95rem;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  margin-bottom: 0;
  font-weight: 400;
}
.service-fade-enter-active,
.service-fade-leave-active {
  transition: all 0.5s cubic-bezier(0.55, 0, 0.1, 1);
}
.service-fade-enter-from,
.service-fade-leave-to {
  opacity: 0;
  transform: scale(0.9);
}
.el-button--danger {
  background-color: #c09153 !important;
  border-color: #c09153 !important;
  padding: 14px 35px;
  font-weight: bold;
}
.el-button--danger:hover {
  background-color: #b38246 !important;
  border-color: #b38246 !important;
}
</style>
