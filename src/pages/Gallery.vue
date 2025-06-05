<template>
  <div class="gallery-page">
    <div class="container py-5">
      <h1 class="gallery-title text-center mb-4">{{ props.pageTitle }}</h1>

      <!-- Filter Controls -->
      <div v-if="!isLoading && props.showFilters && uniqueCategories.length > 0" class="gallery-filters text-center mb-5">
        <!-- Hiển thị el-select trên mobile -->
        <el-select
          v-if="isMobileView"
          v-model="selectedCategory"
          placeholder="Select Category"
          @change="filterImages"
          class="gallery-filter-select"
          size="large"
        >
          <el-option label="All" value=""></el-option>
          <el-option
            v-for="category in uniqueCategories"
            :key="`select-${category}`"
            :label="category"
            :value="category"
          />
        </el-select>

        <!-- Hiển thị el-radio-group trên desktop/tablet -->
        <el-radio-group v-else v-model="selectedCategory" size="large" @change="filterImages">
          <el-radio-button label="">All</el-radio-button>
          <el-radio-button
            v-for="category in uniqueCategories"
            :key="category"
            :label="category"
          >
            {{ category }}
          </el-radio-button>
        </el-radio-group>
      </div>

      <!-- Loading State -->
      <div v-if="isLoading" class="text-center py-5">
        <el-skeleton :rows="5" animated />
        <p class="mt-3 text-muted">Loading gallery...</p>
      </div>

      <!-- Empty State (sau khi filter hoặc không có ảnh ban đầu) -->
      <div v-else-if="displayedImages.length === 0" class="text-center py-5">
        <el-empty :description="emptyStateDescription" />
      </div>

      <!-- Gallery Grid -->
      <div v-else class="row">
        <div
          v-for="(image, index) in displayedImages"
          :key="image.id || `gallery-img-${index}`"
          class="col-lg-4 col-md-6 col-sm-12 mb-4 gallery-col"
        >
          <el-card shadow="hover" :body-style="{ padding: '0px' }" class="gallery-item-card h-100">
            <el-image
              :src="image.src"
              :alt="image.alt || 'Gallery image'"
              fit="cover"
              class="gallery-image"
              lazy
              scroll-container=".gallery-page"
            >
              <template #placeholder>
                <div class="image-slot">Loading<span class="dot">.</span><span class="dot">.</span><span class="dot">.</span></div>
              </template>
              <template #error>
                <div class="image-slot">
                  <el-icon :size="30"><Picture /></el-icon>
                  <span>Error</span>
                </div>
              </template>
            </el-image>
            <div v-if="image.caption" class="gallery-caption p-3 text-center">
              <span class="fw-medium">{{ image.caption }}</span>
            </div>
          </el-card>
        </div>
      </div>

      <!-- Load More Button -->
      <div v-if="!isLoading && filteredImages.length > displayedImages.length" class="text-center mt-4">
        <el-button type="primary" plain size="large" @click="loadMoreImages" :loading="isLoadingMore">
          Load More Images ({{ filteredImages.length - displayedImages.length }} remaining)
        </el-button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount, defineProps, watch } from 'vue';
import { Picture } from '@element-plus/icons-vue';

const props = defineProps({
  initialImages: {
    type: Array,
    default: () => []
  },
  isLoadingProp: {
    type: Boolean,
    default: false
  },
  pageTitle: {
    type: String,
    default: 'Our Gallery'
  },
  showFilters: {
    type: Boolean,
    default: true
  },
  itemsPerPage: {
    type: Number,
    default: 9
  }
});

const internalLoading = ref(true);
const isLoadingMore = ref(false);
const allImages = ref([]);
const selectedCategory = ref('');
const itemsToShow = ref(props.itemsPerPage);

const isMobileView = ref(false);
let mediaQueryList = null;

const checkMobileView = (event) => {
  isMobileView.value = event.matches;
};

const isLoading = computed(() => props.isLoadingProp || internalLoading.value);

const processImageData = (images) => {
  if (!Array.isArray(images)) return [];
  return images.map(img => ({
    id: img.id || Date.now() + Math.random(),
    src: img.src,
    alt: img.alt || 'Gallery Image',
    caption: img.caption || '',
    category: img.category || 'General'
  }));
};

const filteredImages = computed(() => {
  if (!selectedCategory.value) {
    return allImages.value;
  }
  return allImages.value.filter(image => image.category === selectedCategory.value);
});

const displayedImages = computed(() => {
  return filteredImages.value.slice(0, itemsToShow.value);
});

const uniqueCategories = computed(() => {
  const categories = new Set(allImages.value.map(image => image.category));
  return Array.from(categories).sort();
});

const emptyStateDescription = computed(() => {
  if (selectedCategory.value && filteredImages.value.length === 0) {
    return `No images found in the "${selectedCategory.value}" category. Try a different filter.`;
  }
  return 'No images to display at the moment. Please check back later!';
});

const filterImages = () => {
  itemsToShow.value = props.itemsPerPage;
};

const loadMoreImages = async () => {
  isLoadingMore.value = true;
  await new Promise(resolve => setTimeout(resolve, 300));
  itemsToShow.value += props.itemsPerPage;
  isLoadingMore.value = false;
};

const loadGalleryData = async () => {
  internalLoading.value = true;
  if (props.initialImages && props.initialImages.length > 0) {
    allImages.value = processImageData(props.initialImages);
  } else {
    await new Promise(resolve => setTimeout(resolve, 1000));
    allImages.value = processImageData([
      { id: 1, src: 'https://images.unsplash.com/photo-1505826759037-406b40feb4cd?q=80&w=1920&auto=format&fit=crop', alt: 'Luxury hotel poolside view', caption: 'Poolside Serenity', category: 'Outdoor' },
      { id: 2, src: 'https://images.unsplash.com/photo-1566073771259-6a8506099945?q=80&w=1920&auto=format&fit=crop', alt: 'Modern hotel lobby', caption: 'Elegant Lobby', category: 'Indoor' },
      { id: 3, src: 'https://images.unsplash.com/photo-1520250497591-112f2f40a3f4?q=80&w=1920&auto=format&fit=crop', alt: 'Breakfast by the pool', caption: 'Morning Bliss', category: 'Dining' },
      { id: 4, src: 'https://images.unsplash.com/photo-1582719508461-905c673771fd?q=80&w=1920&auto=format&fit=crop', alt: 'Comfortable hotel room bed', caption: 'Comfortable Stay', category: 'Rooms' },
      { id: 5, src: 'https://images.unsplash.com/photo-1561501900-3701fa6a0864?q=80&w=1920&auto=format&fit=crop', alt: 'Hotel exterior with palm trees', caption: 'Tropical Paradise', category: 'Outdoor' },
      { id: 6, src: 'https://images.unsplash.com/photo-1590073242678-70ee3fc28e8e?q=80&w=1920&auto=format&fit=crop', alt: 'Hotel restaurant dining area', caption: 'Fine Dining', category: 'Dining' },
      { id: 7, src: 'https://images.unsplash.com/photo-1540541338287-41700207dee6?q=80&w=1920&auto=format&fit=crop', alt: 'Resort view with multiple pools', caption: 'Resort Getaway', category: 'Outdoor' },
      { id: 8, src: 'https://images.unsplash.com/photo-1611892440504-42a792e24d32?q=80&w=1920&auto=format&fit=crop', alt: 'Spacious hotel suite', caption: 'Luxury Suite', category: 'Rooms' },
      { id: 9, src: 'https://images.unsplash.com/photo-1571003123894-1f0594d2b5d9?q=80&w=1920&auto=format&fit=crop', alt: 'Hotel spa and wellness area', caption: 'Relax and Rejuvenate', category: 'Wellness' },
      { id: 10, src: 'https://images.unsplash.com/photo-1551882547-ff40c63fe5fa?q=80&w=1920&auto=format&fit=crop', alt: 'Hotel Gym', caption: 'Fitness Center', category: 'Wellness' },
      { id: 11, src: 'https://i.pinimg.com/736x/ee/3f/42/ee3f42ae97d810aa1e73c78d2c95cba1.jpg', alt: 'Hotel Bar', caption: 'Evening Drinks', category: 'Dining' },
      { id: 12, src: 'https://images.unsplash.com/photo-1608198093002-ad4e005484ec?q=80&w=1920&auto=format&fit=crop', alt: 'Room with a View', caption: 'Breathtaking Views', category: 'Rooms' },
    ]);
  }
  internalLoading.value = false;
};

watch(() => props.initialImages, (newVal) => {
  allImages.value = processImageData(newVal || []);
  itemsToShow.value = props.itemsPerPage;
  selectedCategory.value = '';
  if (!props.isLoadingProp) {
    internalLoading.value = false;
  }
}, { deep: true, immediate: true });

watch(() => props.isLoadingProp, (newVal) => {
  internalLoading.value = newVal;
  if (newVal === false && props.initialImages && props.initialImages.length > 0 && allImages.value.length === 0) {
    allImages.value = processImageData(props.initialImages);
  }
}, { immediate: true });

onMounted(() => {
  if (typeof window !== 'undefined') {
    mediaQueryList = window.matchMedia('(max-width: 767.98px)');
    isMobileView.value = mediaQueryList.matches;
    mediaQueryList.addEventListener('change', checkMobileView);
  }

  if (!props.isLoadingProp && allImages.value.length === 0) {
    loadGalleryData();
  } else if (!props.isLoadingProp && allImages.value.length > 0){
    internalLoading.value = false;
  }
});

onBeforeUnmount(() => {
  if (mediaQueryList) {
    mediaQueryList.removeEventListener('change', checkMobileView);
  }
});

</script>

<style scoped>
.gallery-page {
  background-color: #f8f9fa;
  min-height: 100vh;
}

.gallery-title {
  font-weight: 300;
  color: #2c3e50;
  font-family: 'Georgia', serif;
}

.gallery-filters .el-radio-button__inner {
  font-size: 0.9rem;
}

.gallery-filter-select {
  width: 100%;
  max-width: 300px; /* Hoặc một giá trị khác bạn thấy phù hợp */
  margin-left: auto;
  margin-right: auto;
  display: block; /* Để margin auto hoạt động */
}

.gallery-col {
  display: flex;
}

.gallery-item-card {
  border-radius: 8px;
  overflow: hidden;
  transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
  display: flex;
  flex-direction: column;
  width: 100%;
}

.gallery-item-card:hover {
  transform: translateY(-5px) scale(1.02);
  box-shadow: 0 12px 25px rgba(0, 0, 0, 0.18) !important;
}

.gallery-image {
  width: 100%;
  height: 250px;
  display: block;
  object-fit: cover;
}

.gallery-caption {
  background-color: #fff;
  border-top: 1px solid #eee;
  margin-top: auto;
  min-height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.gallery-caption span {
  color: #555;
  font-size: 0.9rem;
}

.image-slot {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  min-height: 250px;
  background: #eef1f5;
  color: #a8b1c2;
  font-size: 14px;
}

.image-slot .el-icon {
  font-size: 30px;
  margin-bottom: 8px;
}

.dot {
  animation: dot 1.4s infinite ease-in-out both;
  display: inline-block;
}
.dot:nth-child(1) { animation-delay: -0.32s; }
.dot:nth-child(2) { animation-delay: -0.16s; }
.dot:nth-child(3) { animation-delay: 0s; }

@keyframes dot {
  0%, 80%, 100% { opacity: 0; transform: scale(0.8); }
  40% { opacity: 1; transform: scale(1); }
}


</style>