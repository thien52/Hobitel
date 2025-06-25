<template>
  <div class="promotions-page container py-5">
    <h1 class="text-center mb-5 page-title">All Offers & Promotions</h1>

    <div class="row g-4 justify-content-center">
      <div
        class="col-12 col-md-6 col-lg-4"
        v-for="promo in allPromotions"
        :key="promo.id"
      >
        <el-card
          shadow="hover"
          class="promotion-item-card d-flex justify-content-between"
        >
          <template #header>
            <div class="card-header-image">
              <img :src="promo.image" :alt="promo.title" class="card-img-top" />
            </div>
          </template>
          <div class="card-body">
            <h5 class="card-title">{{ promo.title }}</h5>
            <p class="card-text">{{ promo.description }}</p>
            <div class="card-footer-actions">
              <el-button
                type="primary"
                plain
                class="w-100"
                @click="navigateToDetail(promo.id)"
              >
                View Offer Details
              </el-button>
            </div>
          </div>  
        </el-card>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router"; // Import useRouter to use the router
import axios from "@/axios";
const router = useRouter(); // Get the router instance

// Sample data for ALL promotions (IDs should match those in PromotionsSection.vue)
// This is where you would manage the entire list of promotions.
const allPromotions = ref([]);
const fetchPromotion = async () => {
  try {
    const response = await axios.get("/promotion");
    allPromotions.value = response.data;
    console.log(" Dữ liệu promotion nhận được:", response.data); // Thêm dòng này
  } catch (error) {
    console.error("Lỗi tải dữ liệu promotion:", error);
  }
};
// Function to navigate to the promotion detail page
const navigateToDetail = (promoId) => {
  router.push(`/promotions/${promoId}`); // Navigates to /promotions/THE_PROMOTION_ID
};
onMounted(async () => {
  fetchPromotion();
});
</script>

<style scoped>
.promotions-page {
  min-height: calc(
    100vh - var(--header-height, 60px) - var(--footer-height, 200px)
  ); /* Adjust according to your header/footer height */
  background-color: #f0f2f5;
  padding-top: 5rem;
  padding-bottom: 5rem;
}

.page-title {
  font-size: 3rem;
  font-weight: bold;
  color: #2c3e50;
  margin-bottom: 3rem;
}

.promotion-item-card {
  height: 100%;
  display: flex;
  flex-direction: column;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.promotion-item-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.card-header-image {
  height: 220px; /* Fixed height for the image */
  overflow: hidden;
}

.card-img-top {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.promotion-item-card:hover .card-img-top {
  transform: scale(1.05);
}

.promotion-item-card .card-body {
  flex-grow: 1;
  padding: 20px;
  display: flex;
  flex-direction: column;
}

.promotion-item-card .card-title {
  font-size: 1.6rem;
  font-weight: bold;
  color: #333;
  margin-bottom: 0.75rem;
  line-height: 1.3;
}

.promotion-item-card .card-text {
  font-size: 1rem;
  color: #555;
  line-height: 1.6;
  flex-grow: 1;
  margin-bottom: 1rem;
}

.card-footer-actions {
  margin-top: auto;
}

.el-button.w-100 {
  height: 48px;
  font-weight: bold;
  font-size: 1.1rem;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .page-title {
    font-size: 2.5rem;
  }
  .promotion-item-card .card-title {
    font-size: 1.4rem;
  }
  .promotion-item-card .card-text {
    font-size: 0.9rem;
  }
}

@media (max-width: 576px) {
  .page-title {
    font-size: 2rem;
  }
}
</style>
