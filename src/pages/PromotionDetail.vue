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
import { ArrowLeft, Calendar } from '@element-plus/icons-vue'; // Import icons for buttons

const route = useRoute(); // Get the current route object to access params
const router = useRouter(); // Get the router object for navigation

const promotion = ref(null);

// Sample full data for all promotions (IDs should match those in PromotionsSection.vue and Promotions.vue)
// In a real application, this data would typically be fetched from an API based on the 'id'
const allPromotionsData = [
  {
    id: 'summer-deal',
    image: 'https://i.pinimg.com/736x/bf/8a/2e/bf8a2efd2e72a2a39e844648e22e91b3.jpg',
    title: 'Vibrant Summer Getaway Package',
    description: 'Enjoy a 20% discount on all rooms for stays of 3 nights or more, including free breakfast and a spa voucher.',
    full_description: 'Enjoy a vibrant summer vacation at our hotel with this special offer package. The package includes: 3 nights in a Deluxe room with a city or sea view, daily buffet breakfast at the restaurant, one 500,000 VND voucher for spa services at The Serene Spa, and free access to the infinity pool. \n\nApplicable for bookings and stays from June 1, 2025, to August 31, 2025. Cannot be combined with other promotions. A 7-day advance booking is required. Contact reception for more details on other terms and conditions.',
    startDate: '06/01/2025',
    endDate: '08/31/2025',
    terms: 'Non-refundable, non-changeable.'
  },
  {
    id: 'spa-dining',
    image: 'https://i.pinimg.com/736x/c7/3d/f1/c73df1607525aff076e74fd999174bde.jpg',
    title: 'Ultimate Spa & Dining Experience',
    description: 'Relax with a premium spa treatment and enjoy a romantic dinner at our restaurant.',
    full_description: 'The Spa & Dining package is the perfect combination for a romantic evening. The package includes: one 60-minute full-body massage at The Serene Spa, and a premium set menu dinner for two at our rooftop restaurant with panoramic views of the sea and city. \n\nApplicable for couples and requires at least 24 hours advance booking. Valid for use until December 31, 2025. Please contact us to schedule your spa appointment and reserve a table in advance.',
    startDate: '01/01/2025',
    endDate: '12/31/2025',
    terms: 'Advance booking required, not applicable on major holidays.'
  },
  {
    id: 'family-vacation',
    image: 'https://i.pinimg.com/736x/55/09/76/5509762d97ab3128443e748606e53026.jpg',
    title: 'Complete Family Happiness Vacation',
    description: 'Free stay for children under 12, including tickets to the hotel\'s private play area.',
    full_description: 'Your family vacation will be even more memorable with this offer. The package includes: Free stay for 2 children under 12 when sharing a room with parents in a Suite or Executive Suite, free entry to the Kid\'s Club throughout the stay, and a complimentary family pizza lunch at the Pool Bar. \n\nApplicable for bookings from now until September 30, 2025. Please present the children\'s identification upon check-in.',
    startDate: '05/01/2025',
    endDate: '09/30/2025',
    terms: 'Applicable for children under 12, maximum 2 children per room.'
  },
  {
    id: 'early-bird',
    image: 'https://i.pinimg.com/736x/72/31/87/7231873bca08272a68cfb7420792051d.jpg',
    title: 'Early Bird Offer - Maximum Savings',
    description: 'Book 30 days in advance to receive up to 15% off on all room types.',
    full_description: 'Plan your trip early to get the best deals. Get a 15% discount for bookings made 30 days before the check-in date, and a 10% discount for bookings made 15 days in advance. Applicable to all room types and not limited by length of stay. \n\nThis promotion cannot be combined with other offers. Free cancellation is available up to 7 days before the check-in date.',
    startDate: 'Ongoing',
    endDate: 'Ongoing',
    terms: 'Must book by the specified number of days in advance to receive the offer.'
  }
];

// This function is called when the component is mounted
onMounted(() => {
  const promoId = route.params.id; // Get the promotion ID from the URL (e.g., 'summer-deal' from /promotions/summer-deal)
  promotion.value = allPromotionsData.find(p => p.id === promoId); // Find the promotion in the sample data
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