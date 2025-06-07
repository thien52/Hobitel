<template>
  <div class="promotions-page container py-5">
    <h1 class="text-center mb-5 page-title">All Offers & Promotions</h1>

    <div class="row g-4 justify-content-center">
      <div class="col-12 col-md-6 col-lg-4" v-for="promo in allPromotions" :key="promo.id">
        <el-card shadow="hover" class="promotion-item-card">
          <template #header>
            <div class="card-header-image">
              <img :src="promo.image" :alt="promo.title" class="card-img-top">
            </div>
          </template>
          <div class="card-body">
            <h5 class="card-title">{{ promo.title }}</h5>
            <p class="card-text">{{ promo.description }}</p>
            <div class="card-footer-actions">
              <el-button type="primary" plain class="w-100" @click="navigateToDetail(promo.id)">
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
import { ref } from 'vue';
import { useRouter } from 'vue-router'; // Import useRouter to use the router

const router = useRouter(); // Get the router instance

// Sample data for ALL promotions (IDs should match those in PromotionsSection.vue)
// This is where you would manage the entire list of promotions.
const allPromotions = ref([
  {
    id: 'summer-deal', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/bf/8a/2e/bf8a2efd2e72a2a39e844648e22e91b3.jpg',
    title: 'Vibrant Summer Getaway Package',
    description: 'Enjoy a 20% discount on all rooms for stays of 3 nights or more, including free breakfast and a spa voucher.',
    full_description: 'Summer package details: The package includes a 3-night stay in a Deluxe room, daily buffet breakfast, one 500,000 VND voucher for spa services, and free access to the infinity pool. Applicable for bookings from June 1, 2025, to August 31, 2025. Cannot be combined with other promotions.',
  },
  {
    id: 'spa-dining', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/c7/3d/f1/c73df1607525aff076e74fd999174bde.jpg',
    title: 'Ultimate Spa & Dining Experience',
    description: 'Relax with a premium spa treatment and enjoy a romantic dinner at our restaurant.',
    full_description: 'Spa & Dining package details: The package includes one 60-minute full-body massage, a romantic set menu dinner for two at the rooftop restaurant with sea views. Applicable for couples and requires 24-hour advance booking. Valid until December 31, 2025.',
  },
  {
    id: 'family-vacation', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/55/09/76/5509762d97ab3128443e748606e53026.jpg',
    title: 'Complete Family Happiness Vacation',
    description: 'Free stay for children under 12, including tickets to the hotel\'s private play area.',
    full_description: 'Family package details: Free stay for 2 children under 12 when sharing a room with parents, free entry to the Kid\'s Club, and a complimentary family pizza lunch. Applicable for Suite bookings and above. Valid until September 30, 2025.',
  },
  {
    id: 'early-bird', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/72/31/87/7231873bca08272a68cfb7420792051d.jpg',
    title: 'Early Bird Offer - Maximum Savings',
    description: 'Book 30 days in advance to receive up to 15% off on all room types.',
    full_description: 'Early bird offer details: 15% discount for bookings made 30 days in advance, 10% for bookings 15 days in advance. Applicable to all room types and not limited by length of stay. Cannot be combined with other promotions. Free cancellation up to 7 days before check-in.',
  },
  // Add other promotions here to display them on this page
]);

// Function to navigate to the promotion detail page
const navigateToDetail = (promoId) => {
  router.push(`/promotions/${promoId}`); // Navigates to /promotions/THE_PROMOTION_ID
};
</script>

<style scoped>
.promotions-page {
  min-height: calc(100vh - var(--header-height, 60px) - var(--footer-height, 200px)); /* Adjust according to your header/footer height */
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