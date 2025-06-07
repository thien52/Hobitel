<template>
  <div class="service-page container py-5">
    <h1 class="page-header-title text-center mb-5">Explore Our Comprehensive Services</h1>
    <p class="lead text-center text-muted mb-5">
      Discover a world of comfort and convenience designed to make your stay unforgettable.
    </p>

    <div
      v-for="(service, index) in services"
      :key="service.id"
      class="row align-items-center mb-5 service-item"
    >
      <div
        :class="[
          'col-12 col-md-6',
          index % 2 !== 0 ? 'order-md-2' : 'order-md-1'
        ]"
        class="mb-4 mb-md-0"
      >
        <el-image
          :src="service.imageSrc"
          :alt="service.title"
          fit="cover"
          class="img-fluid rounded shadow-sm w-100 service-image"
          lazy
        >
          <template #placeholder>
            <div class="image-slot">Loading<span class="dot">...</span></div>
          </template>
          <template #error>
            <div class="image-slot">
              <el-icon><icon-picture /></el-icon>
              <span>Image failed to load</span>
            </div>
          </template>
        </el-image>
      </div>

      <div
        :class="[
          'col-12 col-md-6',
          index % 2 !== 0 ? 'order-md-1' : 'order-md-2'
        ]"
      >
        <div class="p-4 p-lg-5 bg-light rounded service-content">
          <span class="text-uppercase fw-bold service-category">Our Services</span>
          <h2 class="display-6 fw-bold mt-1 mb-3 service-title">{{ service.title }}</h2>
          <p class="text-muted mb-4 service-description">{{ service.description }}</p>
          <div class="d-flex flex-wrap">
            <el-button type="danger" size="large" @click="viewDetails(service)" class="mb-2 me-2">
              VIEW DETAIL
            </el-button>
            <el-button type="primary" size="large" @click="addServiceToCart(service)" class="mb-2">
              USE SERVICE
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <div v-if="selectedServices.length > 0" class="mt-5 p-4 bg-white shadow-sm rounded selected-services-summary">
      <h3 class="mb-4 text-center service-summary-title">Your Selected Services</h3>
      <el-table :data="selectedServices" style="width: 100%" class="mb-3">
        <el-table-column prop="title" label="Service" min-width="180" />
        <el-table-column label="Price" width="120">
          <template #default="scope">
            ${{ scope.row.price.toFixed(2) }}
          </template>
        </el-table-column>
        <el-table-column label="Quantity" width="150">
          <template #default="scope">
            <el-input-number
              v-model="scope.row.quantity"
              :min="1"
              size="small"
              @change="(currentValue) => handleQuantityChange(scope.row.id, currentValue)"
              controls-position="right"
              style="width: 100px;"
            />
          </template>
        </el-table-column>
        <el-table-column label="Subtotal" width="120">
          <template #default="scope">
            ${{ (scope.row.price * scope.row.quantity).toFixed(2) }}
          </template>
        </el-table-column>
        <el-table-column label="Actions" width="100" align="center">
          <template #default="scope">
            <el-button type="danger" size="small" @click="removeItemFromCart(scope.row.id)" plain circle>
              <el-icon><icon-delete /></el-icon>
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="text-end mt-4 d-flex justify-content-between align-items-center">
        <el-button type="info" plain @click="clearSelectedServices">
          <el-icon class="me-1"><CircleClose /></el-icon> Clear All
        </el-button>
        <h4 class="fw-bold mb-0">Total Cost: <span class="text-danger">${{ totalCost.toFixed(2) }}</span></h4>
      </div>
    </div>

    <div v-else class="mt-5 p-4 bg-white shadow-sm rounded selected-services-summary text-center text-muted">
      <p class="mb-0">No services selected yet. Click "USE SERVICE" to add items to your summary.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import { ElMessage } from 'element-plus'; // Removed ElLoading as no async data fetch
import { Delete as IconDelete, Picture as IconPicture, CircleClose } from '@element-plus/icons-vue'; // Removed Loading icon

// Hardcoded services data
const services = ref([
  {
    id: 1,
    imageSrc: 'https://hibtel.webhotel.vn/assets/image/facilities-1.jpg',
    title: 'Exquisite Dining Experience',
    description: 'Savor gourmet dishes prepared by our award-winning chefs. Our restaurant offers a unique blend of local flavors and international cuisine in an elegant setting. Perfect for a romantic dinner or a special celebration.',
    price: 75.00,
  },
  {
    id: 2,
    imageSrc: 'https://images.unsplash.com/photo-1544161515-4ab6ce6db874?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8NHx8c3BhJTIwdHJlYXRtZW50fGVufDB8fDB8fHww&auto=format&fit=crop&w=800&q=60',
    title: 'Relaxing Spa & Wellness Treatments',
    description: 'Unwind and rejuvenate with our wide range of spa treatments. From soothing massages to invigorating facials, our expert therapists will ensure a blissful experience for your mind and body.',
    price: 120.00,
  },
  {
    id: 3,
    imageSrc: 'https://i.pinimg.com/736x/57/32/1b/57321b3fca7242ba52da622d3b079b35.jpg',
    title: 'Modern Conference Facilities',
    description: 'Host your next event in our fully equipped conference rooms. With state-of-the-art audiovisual technology and flexible layouts, we provide the perfect environment for successful meetings, seminars, and workshops.',
    price: 200.00,
  },
  {
    id: 4,
    imageSrc: 'https://i.pinimg.com/736x/20/42/d5/2042d5e371ed18dd1a7de511b5dbdd81.jpg',
    title: 'State-of-the-Art Fitness Center',
    description: 'Maintain your fitness routine with our modern fitness center. Equipped with a variety of cardio machines, weightlifting equipment, and dedicated spaces for yoga and stretching.',
    price: 30.00, // Example price for daily access
  },
  {
    id: 5,
    imageSrc: 'https://i.pinimg.com/736x/67/4c/8c/674c8c699db95d18e0a19fdd3fff8709.jpg',
    title: 'Refreshing Swimming Pool Access',
    description: 'Dive into relaxation at our expansive swimming pool. Whether for a leisurely swim or fun with family, our pool area offers a refreshing escape with comfortable seating and poolside service.',
    price: 25.00, // Example price for daily access
  },
]);

const selectedServices = ref([]); // Stores { id, title, price, quantity }

onMounted(() => {
  window.scrollTo(0, 0); // Scroll to top on mount
});

const viewDetails = (service) => {
  console.log('View details for:', service.title);
  // In a real application, you'd navigate to a detail page or open a modal
  ElMessage.info(`Viewing details for: ${service.title}`);
};

const addServiceToCart = (service) => {
  const existingItem = selectedServices.value.find(item => item.id === service.id);
  if (existingItem) {
    existingItem.quantity++;
    ElMessage.success(`${service.title} quantity updated to ${existingItem.quantity}.`);
  } else {
    selectedServices.value.push({
      id: service.id,
      title: service.title,
      price: service.price,
      quantity: 1
    });
    ElMessage.success(`${service.title} added to your selected services.`);
  }
};

const handleQuantityChange = (itemId, newQuantity) => {
  const item = selectedServices.value.find(item => item.id === itemId);
  if (item) {
    item.quantity = newQuantity;
    ElMessage.success(`Quantity for ${item.title} changed to ${newQuantity}.`);
  }
};

const removeItemFromCart = (itemId) => {
  const itemIndex = selectedServices.value.findIndex(item => item.id === itemId);
  if (itemIndex !== -1) {
    const itemTitle = selectedServices.value[itemIndex].title;
    selectedServices.value.splice(itemIndex, 1);
    ElMessage.warning(`${itemTitle} removed from your services.`);
  }
};

const clearSelectedServices = () => {
    selectedServices.value = [];
    ElMessage.info('All selected services have been cleared.');
};

const totalCost = computed(() => {
  return selectedServices.value.reduce((acc, item) => acc + (item.price * item.quantity), 0);
});
</script>

<style scoped>
/* Bootstrap container and spacing classes are already good: .container, .py-5, .mb-5 */
/* Bootstrap responsive classes are also used in template: col-12, col-md-6, order-md-1, order-md-2, mb-4, mb-md-0 */

.container {
  max-width: 1140px; /* Standard Bootstrap large container width */
}

.page-header-title {
  font-family: 'Playfair Display', serif;
  font-size: 3rem;
  font-weight: 700;
  color: #1A3760;
  line-height: 1.2;
}

.service-item:last-child {
  margin-bottom: 0 !important; /* Remove margin from the last service item before summary */
}

.service-image {
  border-radius: 0.5rem; /* Consistent rounding */
  object-fit: cover;
  min-height: 300px;
  max-height: 400px; /* Limit image height */
  width: 100%;
}

.service-content {
  height: 100%; /* Ensure content box takes full height */
  display: flex;
  flex-direction: column;
  justify-content: center; /* Vertically center content */
}

.service-category {
  color: #C09153; /* Consistent primary accent color */
  font-size: 0.85rem;
  letter-spacing: 0.05em;
  font-weight: 700 !important;
}

.service-title {
  font-family: 'Georgia', serif;
  color: #333;
  font-size: 2.2rem; /* Adjusted for better hierarchy */
}

.service-description {
  line-height: 1.6;
  font-size: 1rem;
  color: #666;
}

/* El-image placeholder and error styles */
.image-slot {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  min-height: 300px; /* Ensure placeholder has height */
  background: #f5f7fa;
  color: var(--el-text-color-secondary);
  font-size: 14px;
}
.image-slot .el-icon {
  font-size: 30px;
  margin-bottom: 8px;
}
.dot {
  animation: dot 1.4s infinite ease-in-out both;
  animation-delay: -0.32s;
}
.dot:nth-child(2) {
  animation-delay: -0.16s;
}
.dot:nth-child(3) {
  animation-delay: 0s;
}
@keyframes dot {
  0%, 80%, 100% {
    transform: scale(0);
  }
  40% {
    transform: scale(1.0);
  }
}

/* Buttons */
.el-button--danger {
    background-color: #C09153 !important; /* Primary button color */
    border-color: #C09153 !important;
    color: #fff !important;
}
.el-button--danger:hover {
    background-color: #b38246 !important;
    border-color: #b38246 !important;
}
.el-button--primary { /* For "USE SERVICE" */
    background-color: #1A3760 !important; /* Darker accent color */
    border-color: #1A3760 !important;
    color: #fff !important;
}
.el-button--primary:hover {
    background-color: #0d2740 !important;
    border-color: #0d2740 !important;
}
.el-button--large {
  padding: 12px 25px;
  font-size: 1rem;
  font-weight: 600;
  border-radius: 6px;
}

/* Selected Services Summary */
.selected-services-summary {
  border: 1px solid #e0e0e0;
  background-color: #fff;
  border-radius: 0.75rem;
}

.service-summary-title {
  font-family: 'Georgia', serif;
  color: #1A3760;
  font-weight: bold;
}

.selected-services-summary .el-table th {
  background-color: #f8f9fa !important;
  color: #333;
  font-weight: 600;
}

.selected-services-summary .el-button--danger.is-plain.is-circle { /* For delete button */
    color: #dc3545 !important;
    border-color: #dc3545 !important;
}
.selected-services-summary .el-button--danger.is-plain.is-circle:hover {
    background-color: #dc3545 !important;
    color: white !important;
}

.selected-services-summary .el-button--info.is-plain { /* Clear All button */
    color: #6c757d;
    border-color: #6c757d;
}
.selected-services-summary .el-button--info.is-plain:hover {
    background-color: #6c757d;
    color: white;
}


/* Responsive adjustments based on Bootstrap 5 breakpoints */
/* No need for @media (max-width: 991.98px) for col-md-6, as bootstrap handles this */

@media (max-width: 767.98px) { /* Small devices (sm) and down */
  .page-header-title {
    font-size: 2rem;
  }
  .service-title {
    font-size: 1.6rem;
  }
  .service-description {
    font-size: 0.9rem;
  }
  .el-button--large {
    padding: 10px 18px;
    font-size: 0.9rem;
    flex-grow: 1; /* Make buttons take full width if needed on very small screens */
  }
  .d-flex.flex-wrap {
    justify-content: center; /* Center buttons on small screens */
  }
  .service-item {
    margin-bottom: 3rem !important; /* Adjust margin for mobile */
  }
  /* Force image then text order on small screens for all items */
  .service-item > div {
      order: 1 !important;
  }
  .service-item > div:nth-child(2) {
      order: 2 !important;
  }
}
</style>