<template>
  <section class="about-section container my-5">
    <div class="row align-items-center">
      <div class="col-lg-6 mb-4 mb-lg-0">
        <div class="image-wrapper">
          <img
            :src="mainImageUrl"
            :alt="imageAlt"
            class="img-fluid rounded-3 shadow-lg main-about-image"
          />
          <img
            v-if="secondaryImageUrl"
            :src="secondaryImageUrl"
            :alt="secondaryImageAlt || 'Secondary image'"
            class="img-fluid rounded-3 shadow-lg secondary-about-image"
          />
        </div>
      </div>

      <div class="col-lg-6 ps-lg-5">
        <p class="text-uppercase fw-bold about-us-label">
          {{ subHeading }}
        </p>
        <h2 class="display-5 fw-semibold mb-3 main-heading">
          {{ mainHeading }}
        </h2>
        <p class="text-muted mb-4 lead">
          {{ description }}
        </p>

        <div
          v-if="promotionalMessage"
          role="alert"
          class="alert alert-info p-3 mb-4 promotion-alert"
        >
          <div class="d-flex align-items-center">
            <el-icon
              v-if="promotionIcon"
              :size="24"
              class="me-2 flex-shrink-0 promotion-icon"
            >
              <component :is="promotionIcon" />
            </el-icon>
            <span>{{ promotionalMessage }}</span>
          </div>
        </div>

        <div v-if="features.length > 0" class="row mb-4 g-3">
          <div
            v-for="(feature, index) in features"
            :key="index"
            :class="features.length === 1 ? 'col-md-12' : 'col-md-6'"
            class="feature-col"
          >
            <div
              class="d-flex align-items-start feature-item p-3 bg-light rounded-3 h-100"
            >
              <div v-if="feature.icon" class="feature-icon me-3 flex-shrink-0">
                <el-icon :size="35" color="#D9534F">
                  <component :is="feature.icon" />
                </el-icon>
              </div>
              <div>
                <h5 class="mb-1 fw-bold">{{ feature.title }}</h5>
                <p class="small text-muted mb-0">
                  {{ feature.description }}
                </p>
              </div>
            </div>
          </div>
        </div>

        <div v-if="quote.text" class="quote-block p-4 rounded-3 mb-4">
          <el-icon :size="30" class="quote-icon mb-2"
            ><ChatDotSquare
          /></el-icon>
          <p class="mb-2 fst-italic">{{ quote.text }}</p>
          <footer class="blockquote-footer mt-2">{{ quote.author }}</footer>
        </div>

        <el-button
          v-if="ctaButtonText"
          type="danger"
          size="large"
          round
          @click="handleCtaClick"
          class="cta-button mt-3"
        >
          {{ ctaButtonText }}
          <el-icon class="el-icon--right"><Right /></el-icon>
        </el-button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { useRouter } from "vue-router";
import {
  Promotion as DefaultPromotionIcon,
  Star,
  Right,
  Service,
  CoffeeCup,
  ChatDotSquare,
  TrophyBase,
  OfficeBuilding,
} from "@element-plus/icons-vue";

const router = useRouter();

const props = defineProps({
  mainImageUrl: {
    type: String,
    default:
      "https://images.unsplash.com/photo-1566073771259-6a8506099945?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
  },
  secondaryImageUrl: {
    type: String,
    default: "",
  },
  imageAlt: {
    type: String,
    default: "Hotel View - About Our Hotel",
  },
  secondaryImageAlt: {
    type: String,
    default: "Hotel detail view",
  },
  subHeading: {
    type: String,
    default: "ABOUT US",
  },
  mainHeading: {
    type: String,
    default: "Hobitel - Where Your Holiday Becomes Wonderful",
  },
  description: {
    type: String,
    default:
      "At Hobitel, we don't just provide accommodation; we deliver experiences. From luxurious spaces to attentive service, every moment of your stay is meticulously cared for, ensuring a memorable and inspiring holiday.",
  },
  promotionalMessage: {
    type: String,
    default: "Guaranteed best price when booking directly through our website!",
  },
  promotionIcon: {
    type: [Object, String],
    default: DefaultPromotionIcon,
  },
  features: {
    type: Array,
    default: () => [
      {
        icon: Service,
        title: "World-Class Service",
        description:
          "Our dedicated team is ready to serve you 24/7, providing a superior experience.",
      },
      {
        icon: CoffeeCup,
        title: "Exquisite Cuisine",
        description:
          "Discover a unique culinary journey at our restaurants and bars.",
      },
    ],
  },
  quote: {
    type: Object,
    default: () => ({
      text: "",
      author: "",
    }),
  },
  ctaButtonText: {
    type: String,
    default: "Learn More About Us",
  },
  ctaButtonLink: {
    type: String,
    default: "/about",
  },
  onCtaClick: {
    type: Function,
    default: null,
  },
});

const handleCtaClick = () => {
  if (props.onCtaClick) {
    props.onCtaClick();
  } else if (props.ctaButtonLink) {
    if (props.ctaButtonLink.startsWith("http")) {
      window.open(props.ctaButtonLink, "_blank");
    } else {
      router.push(props.ctaButtonLink);
    }
  }
};
</script>

<style scoped>
.about-section {
  padding-top: 5rem;
  padding-bottom: 5rem;
  background-color: #f8f9fa;
  overflow: hidden; /* Kept overflow:hidden for general layout safety */
  position: relative;
}

/* --- Image Styling (for multiple images) --- */
.image-wrapper {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 75%;
}

.main-about-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 10px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
  z-index: 2;
}

.secondary-about-image {
  position: absolute;
  width: 50%;
  height: auto;
  object-fit: cover;
  border-radius: 10px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
  border: 4px solid #fff;
  z-index: 3;

  bottom: -30px;
  right: -30px;
}

/* --- Other existing styles (no changes for this issue) --- */
.about-us-label {
  color: #d9534f;
  font-size: 0.85rem;
  letter-spacing: 1.5px;
  margin-bottom: 0.75rem !important;
}

.main-heading {
  font-family: "Playfair Display", serif;
  color: #1a3760;
  line-height: 1.2;
}

.lead {
  font-size: 1.15rem;
  line-height: 1.7;
  color: #555;
  font-family: "Roboto", sans-serif;
}

.alert-info {
  background-color: #e6f7ff;
  border-color: #91d5ff;
  color: #0050b3;
  font-size: 0.95rem;
  border-left: 5px solid #1890ff;
}

.promotion-icon {
  color: #1890ff;
}

.feature-item {
  background-color: #ffffff !important;
  border: 1px solid #e0e0e0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out; /* Kept hover effect for features */
}

.feature-item:hover {
  transform: translateY(-3px);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.feature-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  width: 55px;
  height: 55px;
}

.feature-item h5 {
  color: #1a3760;
  font-size: 1.1rem;
}

.feature-item p {
  font-size: 0.88rem;
}

.quote-block {
  background-color: #f0f4f7;
  border-left: 5px solid #d9534f;
  color: #34495e;
  font-size: 1.05rem;
  line-height: 1.6;
}
.quote-icon {
  color: #d9534f;
}
.blockquote-footer {
  font-size: 0.85rem;
  color: #6c757d;
}

.cta-button {
  font-weight: 600;
  padding: 0.8rem 2.2rem;
  font-size: 1.05rem;
  letter-spacing: 0.5px;
  transition: all 0.3s ease; /* Kept hover effect for button */
}

.cta-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(217, 83, 79, 0.4);
}

/* Responsive adjustments */
@media (max-width: 991.98px) {
  .main-about-image {
    max-height: 400px;
  }
  .secondary-about-image {
    width: 40%;
    bottom: -20px;
    right: -20px;
  }
  .main-heading {
    font-size: 2.5rem;
  }
  .lead {
    font-size: 1rem;
  }
  .feature-col {
    margin-bottom: 1rem;
  }
}

@media (max-width: 767.98px) {
  .about-section {
    padding-top: 3rem;
    padding-bottom: 3rem;
  }
  .image-wrapper {
    padding-bottom: 100%;
  }
  .main-about-image {
    max-height: 300px;
  }
  .secondary-about-image {
    width: 35%;
    bottom: -15px;
    right: -15px;
  }
  .main-heading {
    font-size: 2rem;
  }
  .feature-item {
    padding: 1.2rem;
  }
  .feature-icon {
    width: 50px;
    height: 50px;
  }
  .el-icon {
    font-size: 30px !important;
  }
  .cta-button {
    padding: 0.7rem 2rem;
    font-size: 1rem;
  }
  .quote-block {
    padding: 1rem;
    font-size: 0.95rem;
  }
}
</style>
