<template>
  <div class="gallery-page">
    <div class="container py-5">
      <h1 class="gallery-title text-center mb-4">{{ props.pageTitle }}</h1>

      <!-- Filter Controls -->
      <div
        v-if="!isLoading && props.showFilters && uniqueCategories.length > 0"
        class="gallery-filters text-center mb-5"
      >
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
        <el-radio-group
          v-else
          v-model="selectedCategory"
          size="large"
          @change="filterImages"
        >
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
          <el-card
            shadow="hover"
            :body-style="{ padding: '0px' }"
            class="gallery-item-card h-100"
          >
            <el-image
              :src="image.src"
              :alt="image.alt || 'Gallery image'"
              fit="cover"
              class="gallery-image"
              lazy
              scroll-container=".gallery-page"
            >
              <template #placeholder>
                <div class="image-slot">
                  Loading<span class="dot">.</span><span class="dot">.</span
                  ><span class="dot">.</span>
                </div>
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
      <div
        v-if="!isLoading && filteredImages.length > displayedImages.length"
        class="text-center mt-4"
      >
        <el-button
          type="primary"
          plain
          size="large"
          @click="loadMoreImages"
          :loading="isLoadingMore"
        >
          Load More Images ({{ filteredImages.length - displayedImages.length }}
          remaining)
        </el-button>
      </div>
    </div>
  </div>
</template>

<script setup>
import {
  ref,
  computed,
  onMounted,
  onBeforeUnmount,
  defineProps,
  watch,
} from "vue";
import { Picture } from "@element-plus/icons-vue";
import axios from "@/axios";

const props = defineProps({
  initialImages: {
    type: Array,
    default: () => [],
  },
  isLoadingProp: {
    type: Boolean,
    default: false,
  },
  pageTitle: {
    type: String,
    default: "Our Gallery",
  },
  showFilters: {
    type: Boolean,
    default: true,
  },
  itemsPerPage: {
    type: Number,
    default: 9,
  },
});

const internalLoading = ref(true);
const isLoadingMore = ref(false);
const allImages = ref([]);
const selectedCategory = ref("");
const itemsToShow = ref(props.itemsPerPage);

const isMobileView = ref(false);
let mediaQueryList = null;

const checkMobileView = (event) => {
  isMobileView.value = event.matches;
};

const isLoading = computed(() => props.isLoadingProp || internalLoading.value);

const processImageData = (images) => {
  if (!Array.isArray(images)) return [];
  return images.map((img) => ({
    id: img.id || Date.now() + Math.random(),
    src: img.src,
    alt: img.alt || "Gallery Image",
    caption: img.caption || "",
    category: img.category || "General",
  }));
};

const filteredImages = computed(() => {
  if (!selectedCategory.value) {
    return allImages.value;
  }
  return allImages.value.filter(
    (image) => image.category === selectedCategory.value
  );
});

const displayedImages = computed(() => {
  return filteredImages.value.slice(0, itemsToShow.value);
});

const uniqueCategories = computed(() => {
  const categories = new Set(allImages.value.map((image) => image.category));
  return Array.from(categories).sort();
});

const emptyStateDescription = computed(() => {
  if (selectedCategory.value && filteredImages.value.length === 0) {
    return `No images found in the "${selectedCategory.value}" category. Try a different filter.`;
  }
  return "No images to display at the moment. Please check back later!";
});

const filterImages = () => {
  itemsToShow.value = props.itemsPerPage;
};

const loadMoreImages = async () => {
  isLoadingMore.value = true;
  await new Promise((resolve) => setTimeout(resolve, 300));
  itemsToShow.value += props.itemsPerPage;
  isLoadingMore.value = false;
};

const loadGalleryData = async () => {
  internalLoading.value = true;
  if (props.initialImages && props.initialImages.length > 0) {
    allImages.value = processImageData(props.initialImages);
  } else {
    await new Promise((resolve) => setTimeout(resolve, 1000));
    allImages.value = processImageData([]);
  }
  internalLoading.value = false;
};

const fetchGallery = async () => {
  try {
    const response = await axios.get("/gallery");
    const fetched = processImageData(response.data);
    const initial = processImageData(props.initialImages);
    allImages.value = [...initial, ...fetched];
  } catch (error) {
    console.error("Lỗi tải dữ liệu img:", error);
    await loadGalleryData(); // fallback
  }
};


watch(
  () => props.initialImages,
  (newVal) => {
    allImages.value = processImageData(newVal || []);
    itemsToShow.value = props.itemsPerPage;
    selectedCategory.value = "";
    if (!props.isLoadingProp) {
      internalLoading.value = false;
    }
  },
  { deep: true, immediate: true }
);

watch(
  () => props.isLoadingProp,
  (newVal) => {
    internalLoading.value = newVal;
    if (
      newVal === false &&
      props.initialImages &&
      props.initialImages.length > 0 &&
      allImages.value.length === 0
    ) {
      allImages.value = processImageData(props.initialImages);
    }
  },
  { immediate: true }
);

onMounted(async () => {
  try {
    await fetchGallery();
    internalLoading.value = false;
  } catch (err) {
    await loadGalleryData(); // fallback nếu fetch lỗi
  }

  if (typeof window !== "undefined") {
    mediaQueryList = window.matchMedia("(max-width: 767.98px)");
    isMobileView.value = mediaQueryList.matches;
    mediaQueryList.addEventListener("change", checkMobileView);
  }
});

onBeforeUnmount(() => {
  if (mediaQueryList) {
    mediaQueryList.removeEventListener("change", checkMobileView);
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
  font-family: "Georgia", serif;
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
.dot:nth-child(1) {
  animation-delay: -0.32s;
}
.dot:nth-child(2) {
  animation-delay: -0.16s;
}
.dot:nth-child(3) {
  animation-delay: 0s;
}

@keyframes dot {
  0%,
  80%,
  100% {
    opacity: 0;
    transform: scale(0.8);
  }
  40% {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
