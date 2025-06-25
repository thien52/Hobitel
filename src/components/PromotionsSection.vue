<template>
  <section
    id="promotions-highlight"
    class="promotions-section container-fluid mb-5 py-5"
  >
    <div class="container">
      <h2
        class="text-center mb-4 section-title animate__animated animate__fadeInDown"
      >
        Special Offers & Promotions
      </h2>
      <p
        class="text-center mb-5 section-subtitle animate__animated animate__fadeInDown animate__delay-0.5s"
      >
        Don't miss the chance to enjoy a wonderful vacation with exclusive
        offers from our hotel!
      </p>
      <div v-if="isLoading">
        <el-skeleton animated :rows="3" />
      </div>
      <div class="row g-4 justify-content-center">
        <div
          class="col-12 col-md-6 col-lg-4"
          v-for="(promo, index) in promotions"
          :key="promo.id"
        >
          <el-card
            shadow="hover"
            class="promotion-card animate__animated animate__fadeInUp"
            :style="`animation-delay: ${0.7 + index * 0.2}s;`"
          >
            <template #header>
              <div class="card-header-image">
                <img
                  :src="promo.image"
                  :alt="promo.title"
                  class="card-img-top"
                />
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
                  View Details
                </el-button>
              </div>
            </div>
          </el-card>
        </div>
      </div>

      <div
        class="text-center mt-5 animate__animated animate__fadeInUp animate__delay-1.5s"
      >
        <el-button type="primary" size="large" @click="navigateToList()">
          View All Offers
        </el-button>
      </div>
      <div v-if="!isLoading && promotions.length === 0" class="text-center">
        <el-empty description="No promotions available at the moment." />
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import { useRouter } from "vue-router"; // Import useRouter to use the router
import axios from "@/axios";

const router = useRouter(); // Get the router instance

const promotions = ref([]);
const isLoading = ref(true);
// Function to navigate to the promotion detail page
const navigateToDetail = (promoId) => {
  router.push(`/promotions/${promoId}`); // Navigates to /promotions/THE_PROMOTION_ID
};

// Function to navigate to the list of all promotions
const navigateToList = () => {
  router.push("/promotions"); // Navigates to /promotions
};

onMounted(async () => {
  try {
    const res = await axios.get("http://localhost:3000/promotion?_limit=3");
    promotions.value = res.data;
  } catch (err) {
    console.error("Lỗi khi tải promotion:", err);
  } finally {
    isLoading.value = false;
  }
});
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
  flex-grow: 1;
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
@media (max-width: 992px) {
  /* Landscape tablets */
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

@media (max-width: 768px) {
  /* Portrait tablets / Landscape mobiles */
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

@media (max-width: 576px) {
  /* Portrait mobiles */
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
