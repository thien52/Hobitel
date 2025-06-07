<template>
  <section id="promotions-highlight" class="promotions-section container-fluid mb-5 py-5">
    <div class="container">
      <h2 class="text-center mb-4 section-title animate__animated animate__fadeInDown">Special Offers & Promotions</h2>
      <p class="text-center mb-5 section-subtitle animate__animated animate__fadeInDown animate__delay-0.5s">
        Don't miss the chance to enjoy a wonderful vacation with exclusive offers from our hotel!
      </p>

      <div class="row g-4 justify-content-center">
        <div class="col-12 col-md-6 col-lg-4" v-for="(promo, index) in limitedPromotions" :key="promo.id">
          <el-card shadow="hover" class="promotion-card animate__animated animate__fadeInUp" :style="`animation-delay: ${0.7 + index * 0.2}s;`">
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
                  View Details
                </el-button>
              </div>
            </div>
          </el-card>
        </div>
      </div>

      <div class="text-center mt-5 animate__animated animate__fadeInUp animate__delay-1.5s">
        <el-button type="primary" size="large" @click="navigateToList()">
          View All Offers
        </el-button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useRouter } from 'vue-router'; // Import useRouter to use the router

const router = useRouter(); // Get the router instance

// Sample data for featured promotions on the homepage.
// Ensure each promotion has a unique 'id'.
// This 'id' will be used to navigate to the detail page.
const promotions = ref([
  {
    id: 'summer-deal', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/bf/8a/2e/bf8a2efd2e72a2a39e844648e22e91b3.jpg',
    title: 'Vibrant Summer Getaway Package',
    description: 'Enjoy a 20% discount on all rooms for stays of 3 nights or more, including free breakfast and a spa voucher.',
  },
  {
    id: 'spa-dining', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/c7/3d/f1/c73df1607525aff076e74fd999174bde.jpg',
    title: 'Ultimate Spa & Dining Experience',
    description: 'Relax with a premium spa treatment and enjoy a romantic dinner at our restaurant.',
  },
  {
    id: 'family-vacation', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/55/09/76/5509762d97ab3128443e748606e53026.jpg',
    title: 'Complete Family Happiness Vacation',
    description: 'Free stay for children under 12, including tickets to the hotel\'s private play area.',
  },
  {
    id: 'early-bird', // UNIQUE ID
    image: 'https://i.pinimg.com/736x/72/31/87/7231873bca08272a68cfb7420792051d.jpg',
    title: 'Early Bird Offer - Maximum Savings',
    description: 'Book 30 days in advance to receive up to 15% off on all room types.',
  },
  // Add other promotions if needed, but only the first 3 will be displayed on the homepage
]);

// Limit the number of promotions displayed on the homepage (e.g., the first 3)
const limitedPromotions = computed(() => {
  return promotions.value.slice(0, 3);
});

// Function to navigate to the promotion detail page
const navigateToDetail = (promoId) => {
  router.push(`/promotions/${promoId}`); // Navigates to /promotions/THE_PROMOTION_ID
};

// Function to navigate to the list of all promotions
const navigateToList = () => {
  router.push('/promotions'); // Navigates to /promotions
};
</script>

<style scoped>
/* Styles for the overall section */
.promotions-section {
  background-color: #f8f9fa; /* Light background color to distinguish the section */
  border-top: 1px solid #eee; /* Top border */
  border-bottom: 1px solid #eee; /* Bottom border */
  padding-top: 5rem; /* Top padding */
  padding-bottom: 5rem; /* Bottom padding */
}

/* Style for section title */
.section-title {
  font-size: 2.8rem;
  font-weight: bold;
  color: #333;
  margin-bottom: 1rem;
}

/* Style for section subtitle */
.section-subtitle {
  font-size: 1.25rem;
  color: #666;
  max-width: 800px; /* Limit subtitle width */
  margin-left: auto; /* Center */
  margin-right: auto; /* Center */
  line-height: 1.6;
}

/* Style for each promotion card */
.promotion-card {
  height: 100%; /* Ensure cards have equal height in the same row */
  display: flex;
  flex-direction: column;
  border-radius: 8px;
  overflow: hidden; /* Ensure the image doesn't overflow rounded corners */
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08); /* Gentle box shadow */
  transition: transform 0.3s ease, box-shadow 0.3s ease; /* Smooth transition effect */
}

.promotion-card:hover {
  transform: translateY(-5px); /* Slight lift effect on hover */
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15); /* Increase box shadow on hover */
}

/* Style for the image container in the card's header */
.card-header-image {
  height: 200px; /* Fixed height for the image */
  overflow: hidden;
}

/* Style for the image in the card */
.card-img-top {
  width: 100%;
  height: 100%;
  object-fit: cover; /* Ensure image fills the frame without distortion */
  transition: transform 0.3s ease; /* Transition effect for the image */
}

.promotion-card:hover .card-img-top {
  transform: scale(1.05); /* Slight zoom on the image when hovering the card */
}

/* Style for the card body */
.promotion-card .card-body {
  flex-grow: 1; /* Allow the body to grow and fill available space */
  padding: 20px;
  display: flex;
  flex-direction: column;
}

/* Style for the promotion title in the card */
.promotion-card .card-title {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
  margin-bottom: 0.75rem;
  line-height: 1.3;
}

/* Style for the promotion description in the card */
.promotion-card .card-text {
  font-size: 1rem;
  color: #555;
  line-height: 1.6;
  flex-grow: 1; /* Allow the description to grow, pushing the button down */
  margin-bottom: 1rem; /* Space before the button */
}

/* Style for the action button area at the bottom of the card */
.card-footer-actions {
  margin-top: auto; /* Push the button to the bottom of the card-body */
}

/* Style for the "View Details" button */
.el-button.w-100 {
  height: 48px; /* Fixed height for the button */
  font-weight: bold;
  font-size: 1.1rem;
}

/* Responsive adjustments */
@media (max-width: 992px) { /* Landscape tablets */
  .section-title {
    font-size: 2.5rem;
  }
  .section-subtitle {
    font-size: 1.15rem;
  }
  .promotion-card .card-title {
    font-size: 1.4rem;
  }
  .promotion-card .card-text {
    font-size: 0.95rem;
  }
}

@media (max-width: 768px) { /* Portrait tablets / Landscape mobiles */
  .section-title {
    font-size: 2.2rem;
  }
  .section-subtitle {
    font-size: 1.1rem;
  }
  .promotion-card .card-title {
    font-size: 1.3rem;
  }
  .promotion-card .card-text {
    font-size: 0.9rem;
  }
  .promotions-section {
    padding-top: 3rem;
    padding-bottom: 3rem;
  }
}

@media (max-width: 576px) { /* Portrait mobiles */
  .section-title {
    font-size: 1.8rem;
  }
  .section-subtitle {
    font-size: 1rem;
  }
  .promotion-card .card-title {
    font-size: 1.2rem;
  }
  .promotion-card .card-text {
    font-size: 0.85rem;
  }
  .el-button.w-100 {
    height: 40px;
    font-size: 1rem;
  }
}
</style>