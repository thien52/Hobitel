<template>
  <div class="promotion-detail-page container py-5">
    <el-card v-if="promotion" class="detail-card">
      <template #header>
        <div class="detail-header-image">
          <img :src="promotion.image" :alt="promotion.title" class="detail-img-top">
        </div>
      </template>
      <div class="card-body">
        <h1 class="detail-title">{{ promotion.title }}</h1>
        <p class="detail-description">{{ promotion.full_description }}</p>
        <el-divider />
        <div class="detail-meta">
          <p><strong>Applicable Dates:</strong> {{ promotion.startDate || 'To be updated' }} - {{ promotion.endDate || 'To be updated' }}</p>
          <p><strong>Terms & Conditions:</strong> {{ promotion.terms || 'Please contact us for more details.' }}</p>
        </div>
        <div class="mt-4 d-flex flex-wrap">
          <el-button type="primary" size="large" @click="goBack" class="mb-2 mb-md-0">
            <el-icon><ArrowLeft /></el-icon> Back to Promotions List
          </el-button>
          <el-button type="success" size="large" @click="bookPromotion" class="ms-0 ms-md-3">
            <el-icon><Calendar /></el-icon> Book This Offer Now
          </el-button>
        </div>
      </div>
    </el-card>
    <el-empty v-else description="Promotion not found"></el-empty>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router'; // Import useRoute and useRouter
import axios from "@/axios";

const route = useRoute(); // Get the current route object to access params
const router = useRouter(); // Get the router object for navigation

const promotion = ref(null);

// Sample full data for all promotions (IDs should match those in PromotionsSection.vue and Promotions.vue)
// In a real application, this data would typically be fetched from an API based on the 'id'
const allPromotionsData = ref([]);

const fetchPromotionDetails = async () => {
  try {
    const response = await axios.get("/promotionDetail");
    allPromotionsData.value = response.data;
    console.log(" Dữ liệu promotionDetail nhận được:", response.data); // Thêm dòng này
  } catch (error) {
    console.error("Lỗi tải dữ liệu promotionDetail:", error);
  } 
};
// This function is called when the component is mounted
onMounted(async () => {
  await fetchPromotionDetails(); // Chờ dữ liệu tải xong

  const promoId = route.params.id;
  promotion.value = allPromotionsData.value.find(p => p.id === promoId);
});

// Go back to the promotions list page
const goBack = () => {
  router.push('/promotions');
};

// Handle the book promotion event (can navigate or open a booking modal)
const bookPromotion = () => {
  if (promotion.value) {
    alert(`You have selected to book the offer: "${promotion.value.title}". This is where you would integrate the actual booking logic.`);
    // Example: router.push({ path: '/booking', query: { promoId: promotion.value.id } });
  }
};
</script>

<style scoped>
.promotion-detail-page {
  background-color: #f0f2f5;
  min-height: calc(100vh - var(--header-height, 60px) - var(--footer-height, 200px)); /* Ensure the page has a minimum height */
  padding-top: 5rem;
  padding-bottom: 5rem;
}

.detail-card {
  max-width: 900px; /* Limit the width of the detail card */
  margin: 0 auto; /* Center the card */
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.1); /* Stronger box shadow */
}

.detail-header-image {
  height: 400px; /* Taller height for the detail image */
  overflow: hidden;
  border-bottom: 1px solid #ebeef5;
}

.detail-img-top {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.detail-card .card-body {
  padding: 30px;
}

.detail-title {
  font-size: 2.8rem;
  font-weight: bold;
  color: #2c3e50;
  margin-bottom: 1.5rem;
  line-height: 1.2;
}

.detail-description {
  font-size: 1.1rem;
  color: #555;
  line-height: 1.8;
  white-space: pre-wrap; /* Preserve line breaks in full_description */
}

.detail-meta {
  margin-top: 1.5rem;
  font-size: 1rem;
  color: #666;
}

.detail-meta p {
  margin-bottom: 0.5rem;
}

/* Responsive adjustments */
@media (max-width: 992px) {
  .detail-card {
    max-width: 100%;
    margin: 0 15px; /* Add horizontal margin on mobile */
  }
  .detail-header-image {
    height: 300px;
  }
  .detail-title {
    font-size: 2.2rem;
  }
  .detail-description {
    font-size: 1rem;
  }
  .detail-card .card-body {
    padding: 20px;
  }
}

@media (max-width: 768px) {
  .detail-header-image {
    height: 250px;
  }
  .detail-title {
    font-size: 1.8rem;
  }
  .el-button--large {
    width: 100%; /* Button takes full width */
    margin-left: 0 !important; /* Remove margin-left on mobile */
    margin-bottom: 10px; /* Add bottom margin for the button */
  }
  .d-flex.flex-wrap {
    flex-direction: column; /* Ensure buttons stack on mobile */
    align-items: center;
  }
}

@media (max-width: 576px) {
  .detail-header-image {
    height: 200px;
  }
  .detail-title {
    font-size: 1.5rem;
  }
}
</style>