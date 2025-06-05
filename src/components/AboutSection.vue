<template>
  <div class="about-section container my-5">
    <div class="row align-items-center">
      <!-- Cột trái: Hình ảnh -->
      <div class="col-lg-6 mb-4 mb-lg-0">
        <img
          :src="props.imageUrl" 
          :alt="props.imageAlt"
          class="img-fluid rounded-3 shadow-sm"
        />
      </div>

      <!-- Cột phải: Nội dung Text -->
      <div class="col-lg-6 ps-lg-5">
        <p class="text-uppercase fw-bold about-us-label">{{ props.subHeading }}</p>
        <h2 class="display-5 fw-semibold mb-3 main-heading">
          {{ props.mainHeading }}
        </h2>
        <p class="text-muted mb-4">
          {{ props.description }}
        </p>

        <!-- Thông điệp khuyến mãi (v-if) -->
        <div v-if="props.promotionalMessage" class="alert alert-info p-3 mb-4" role="alert">
          <div class="d-flex align-items-center">
            <el-icon v-if="props.promotionIcon" :size="24" class="me-2 flex-shrink-0">
              <component :is="props.promotionIcon" />
            </el-icon>
            <span>{{ props.promotionalMessage }}</span>
          </div>
        </div>

        <!-- Khối Features -->
        <div v-if="hasFeatures" class="row mb-4">
          <!-- Feature item 1 -->
          <div v-if="props.feature1 && props.feature1.title" :class="featureColumnClass">
            <div class="d-flex align-items-start feature-item p-3 bg-light rounded-3 h-100">
              <div v-if="props.feature1.icon" class="feature-icon me-3 flex-shrink-0">
                <el-icon :size="40" color="#D9534F">
                  <component :is="props.feature1.icon" />
                </el-icon>
              </div>
              <div>
                <h5 class="mb-1">{{ props.feature1.title }}</h5>
                <p class="small text-muted mb-0">
                  {{ props.feature1.description }}
                </p>
              </div>
            </div>
          </div>

          <!-- Feature item 2 (hiển thị có điều kiện v-if) -->
          <div v-if="props.showSecondFeature && props.feature2 && props.feature2.title" class="col-md-6">
            <div class="d-flex align-items-start feature-item p-3 bg-light rounded-3 h-100">
              <div v-if="props.feature2.icon" class="feature-icon me-3 flex-shrink-0">
                 <el-icon :size="40" color="#D9534F">
                   <component :is="props.feature2.icon" />
                  </el-icon>
              </div>
              <div>
                <h5 class="mb-1">{{ props.feature2.title }}</h5>
                <p class="small text-muted mb-0">
                  {{ props.feature2.description }}
                </p>
              </div>
            </div>
          </div>
        </div>

        <el-button
          v-if="props.ctaButtonText"
          type="danger"
          size="large"
          round
          @click="handleCtaClick"
        >
          {{ props.ctaButtonText }}
        </el-button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, defineProps } from 'vue';
import { useRouter } from 'vue-router';
// Import các icon mặc định hoặc icon bạn thường dùng
import { Money, PriceTag, Promotion as DefaultPromotionIcon, Star } from '@element-plus/icons-vue';

const router = useRouter();

const props = defineProps({
  // Content Props
  imageUrl: {
    type: String,
    default: 'https://images.unsplash.com/photo-1566073771259-6a8506099945?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D'
  },
  imageAlt: {
    type: String,
    default: 'Hotel View - About Our Hotel'
  },
  subHeading: {
    type: String,
    default: 'ABOUT US'
  },
  mainHeading: {
    type: String,
    default: 'Our Hotel is The Best Place to Celebrate Life'
  },
  description: {
    type: String,
    default: 'Discover the essence of hospitality at our hotel, where every stay is crafted to be an unforgettable experience. We pride ourselves on exceptional service, luxurious amenities, and a commitment to making your moments memorable.'
  },
  // Promotional Message
  promotionalMessage: {
    type: String,
    default: ''
  },
  promotionIcon: {
    type: [Object, String], // Có thể là component icon hoặc tên string của icon toàn cục
    default: DefaultPromotionIcon
  },
  // Features
  showSecondFeature: {
    type: Boolean,
    default: true
  },
  feature1: {
    type: Object,
    default: () => ({
      icon: Money,
      title: 'Best Rate Guarantee',
      description: 'We ensure you get the most competitive prices for your stay.'
    })
  },
  feature2: {
    type: Object,
    default: () => ({
      icon: PriceTag,
      title: 'Exceptional Service',
      description: 'Our dedicated team is here to cater to your every need, 24/7.'
    })
  },
  // CTA Button
  ctaButtonText: {
    type: String,
    default: 'LEARN MORE ABOUT US'
  },
  ctaButtonLink: { // Link nội bộ hoặc URL bên ngoài
    type: String,
    default: '/about'
  },
  onCtaClick: { // Cho phép truyền vào một hàm callback tùy chỉnh cho nút CTA
    type: Function,
    default: null
  }
});

// Computed property để xác định class cho cột feature
const featureColumnClass = computed(() => {
  const hasFeature1 = props.feature1 && props.feature1.title;
  const hasFeature2 = props.showSecondFeature && props.feature2 && props.feature2.title;

  if (hasFeature1 && hasFeature2) {
    return 'col-md-6 mb-3 mb-md-0'; // Cả hai feature, chia đôi
  }
  if (hasFeature1 && !hasFeature2) {
    return 'col-md-12 mb-3'; // Chỉ feature 1, chiếm toàn bộ
  }
  return ''; // Trường hợp không có feature nào (mặc dù đã có v-if bao ngoài)
});

// Computed property để kiểm tra xem có feature nào để hiển thị không
const hasFeatures = computed(() => {
  const hasFeature1 = props.feature1 && props.feature1.title;
  const hasFeature2 = props.showSecondFeature && props.feature2 && props.feature2.title;
  return hasFeature1 || hasFeature2;
});

// Xử lý click cho nút CTA
const handleCtaClick = () => {
  if (props.onCtaClick) {
    props.onCtaClick();
  } else if (props.ctaButtonLink) {
    if (props.ctaButtonLink.startsWith('http')) {
      window.open(props.ctaButtonLink, '_blank'); // Mở link ngoài trong tab mới
    } else {
      router.push(props.ctaButtonLink); // Điều hướng nội bộ
    }
  }
};
</script>

<style scoped>
.about-us-label {
  color: #d9534f;
  font-size: 0.8rem;
  letter-spacing: 1px;
  margin-bottom: 0.5rem !important;
}

.main-heading {
  font-family: 'Georgia', serif;
  color: #2c3e50;
}

.alert-info {
    background-color: #e9f5ff;
    border-color: #d0eaff;
    color: #00529b;
}
/* Thêm style nếu cần cho hình ảnh hoặc các phần khác */
.img-fluid {
  max-height: 500px; /* Giới hạn chiều cao tối đa của ảnh nếu cần */
  object-fit: cover; /* Đảm bảo ảnh vừa vặn và cắt nếu cần */
}
</style>