<template>
  <section class="contact-info-section py-5">
    <div class="container">
      <div class="row justify-content-center mb-5">
        <div class="col-md-8 text-center">
          <h2
            class="section-title fw-bold mb-3 animate__animated animate__fadeInDown"
          >
            Get In Touch With Us
          </h2>
          <p
            class="section-subtitle animate__animated animate__fadeInDown animate__delay-0.5s"
          >
            We are always ready to listen and support you 24/7.
          </p>
          <div
            class="title-underline-container animate__animated animate__zoomIn animate__delay-1s"
          >
            <div class="title-underline">
              <span class="title-underline-segment red"></span>
              <span class="title-underline-segment gray"></span>
            </div>
          </div>
        </div>
      </div>

      <div class="row text-center">
        <div
          v-for="(item, index) in contactItems"
          :key="item.id"
          class="col-12 col-md-6 col-lg-4 mb-4 mb-lg-0"
        >
          <div
            class="contact-item animate__animated animate__fadeInUp"
            :style="`animation-delay: ${1.2 + index * 0.2}s;`"
          >
            <div class="contact-icon-wrapper mb-3">
              <el-icon :size="28" color="white">
                <component :is="item.iconComponent" />
              </el-icon>
            </div>
            <h5 class="contact-item-title fw-semibold mb-2">
              {{ item.title }}
            </h5>
            <p class="contact-item-detail">
              <a
                v-if="getHref(item)"
                :href="getHref(item)"
                class="text-decoration-none text-muted"
              >
                {{ item.detail }}
              </a>
              <span v-else class="text-muted">{{ item.detail }}</span>
            </p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref } from "vue";
import { Location, Phone, Message } from "@element-plus/icons-vue"; // Import icons

const contactItems = ref([
  {
    id: 1,
    iconComponent: Location,
    title: "Hotel Address",
    detail: "123 Beach Road, Da Nang City, Vietnam",
  },
  {
    id: 2,
    iconComponent: Phone,
    title: "24/7 Support",
    detail: "+84 123 456 789", // Add country code for clarity
  },
  {
    id: 3,
    iconComponent: Message,
    title: "Contact Email",
    detail: "contact@hobitel.com", // Changed to .com for a more international feel
  },
]);

function getHref(item) {
  if (item.title.includes("Support")) return `tel:${item.detail}`;
  if (item.title.includes("Email")) return `mailto:${item.detail}`;
  return null;
}
</script>

<style scoped>
/* GENERAL STYLES */
.contact-info-section {
  overflow: hidden;
  background-color: rgba(255, 255, 255, 0.9); /* Nền sáng nhẹ để dễ đọc */
  background-image: url("https://www.transparenttextures.com/patterns/gray-floral.png");
  background-repeat: repeat;
  background-size: 300px;
  background-position: center;
}

/* SECTION TITLE & SUBTITLE */
.section-title {
  color: #1a3760;
  font-family: "Georgia", "Times New Roman", Times, serif;
  font-size: 2.8rem; /* Increased title size */
  font-weight: bold;
}

.section-subtitle {
  font-size: 1.15rem;
  color: #6c757d;
  margin-bottom: 1.5rem; /* Spacing from the underline */
  font-family: "Roboto", sans-serif; /* More modern font */
}

/* UNDERLINE ANIMATION */
.title-underline-container {
  display: flex;
  justify-content: center;
  margin-top: 0.5rem;
}

.title-underline {
  display: flex;
  height: 4px; /* Slightly thicker */
  width: 150px; /* Slightly longer */
}

.title-underline-segment {
  height: 100%;
}

.title-underline-segment.red {
  background-color: #d8463e;
  width: 30%;
}

.title-underline-segment.gray {
  background-color: #e0e0e0;
  width: 70%;
}

/* CONTACT ITEM STYLES */
.contact-item {
  padding: 2rem; /* Increased padding */
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08); /* Softer box-shadow */
  transition: transform 0.3s ease, box-shadow 0.3s ease; /* Added transition for hover */
  height: 100%; /* Ensures equal height */
  display: flex;
  flex-direction: column;
  justify-content: center; /* Vertically center content */
  align-items: center; /* Horizontally center content */
  text-align: center;
}

.contact-item:hover {
  transform: translateY(-8px); /* Slight lift on hover */
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15); /* Increased box-shadow on hover */
}

/* ICON STYLES */
.contact-icon-wrapper {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  width: 70px; /* Slightly larger */
  height: 70px; /* Slightly larger */
  background-color: #d8463e;
  border-radius: 50%;
  color: white;
  box-shadow: 0 4px 15px rgba(216, 70, 62, 0.4); /* More prominent icon shadow */
  margin-bottom: 1.5rem; /* Increased margin below the icon */
  transition: background-color 0.3s ease; /* Transition for icon background color */
}

.contact-item:hover .contact-icon-wrapper {
  background-color: #c0392b; /* Change icon color on item hover */
  animation: pulse 1s infinite;
}
@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.08);
  }
  100% {
    transform: scale(1);
  }
}

/* TEXT STYLES */
.contact-item-title {
  color: #1a3760; /* Darker color */
  font-size: 1.4rem; /* Larger */
  font-weight: 600; /* Bolder */
  margin-bottom: 0.75rem; /* Spacing from the detail */
  font-family: "Montserrat", sans-serif; /* More prominent font */
}

.contact-item-detail {
  color: #6c757d;
  font-size: 1rem; /* Standard size */
  line-height: 1.6;
  font-family: "Open Sans", sans-serif; /* More readable font */
}

/* RESPONSIVE ADJUSTMENTS */
@media (max-width: 991.98px) {
  /* Tablets and large mobiles */
  .section-title {
    font-size: 2.2rem;
  }
  .section-subtitle {
    font-size: 1rem;
  }
  .contact-item {
    padding: 1.5rem;
  }
  .contact-icon-wrapper {
    width: 60px;
    height: 60px;
  }
  .contact-item-title {
    font-size: 1.2rem;
  }
  .contact-item-detail {
    font-size: 0.9rem;
  }
}

@media (max-width: 767.98px) {
  /* Mobile */
  .section-title {
    font-size: 2rem;
  }
  .section-subtitle {
    font-size: 0.95rem;
  }
  .title-underline {
    width: 100px;
  }
  .contact-item {
    margin-bottom: 2rem; /* Spacing between items on mobile */
  }
  .contact-icon-wrapper {
    width: 50px;
    height: 50px;
    margin-bottom: 1rem;
  }
  .contact-item-title {
    font-size: 1.1rem;
  }
  .contact-item-detail {
    font-size: 0.85rem;
  }
}
</style>
