<template>
  <section ref="gallerySectionRef" class="gallery-section py-5">
    <div class="container">
      <div class="row justify-content-center mb-5">
        <div class="col-md-8 text-center">
          <h2
            class="section-title fw-bold mb-3"
            :class="{ 'animate__animated animate__fadeInDown': isSectionVisible }"
            :style="{ 'animation-delay': '0.2s' }"
          >
            {{ sectionTitle }}
          </h2>
          <p
            class="section-subtitle text-muted"
            :class="{ 'animate__animated animate__fadeInDown': isSectionVisible }"
            :style="{ 'animation-delay': '0.5s' }"
          >
            {{ sectionSubtitle }}
          </p>
          <div
            class="title-underline-container"
            :class="{ 'animate__animated animate__zoomIn': isSectionVisible }"
            :style="{ 'animation-delay': '0.8s' }"
          >
            <div class="title-underline">
              <span class="title-underline-segment red"></span>
              <span class="title-underline-segment gray"></span>
            </div>
          </div>
        </div>
      </div>

      <div v-if="isLoading" class="text-center py-5">
        <el-skeleton :rows="3" animated />
        <p class="mt-3 text-muted">Loading images...</p>
      </div>
      <div v-else-if="displayedImages.length === 0" class="text-center py-5">
        <el-empty description="There are currently no images to display." />
      </div>
      <div v-else class="row justify-content-center g-3">
        <div
          v-for="(image, index) in displayedImages"
          :key="image.id || `gallery-section-img-${index}`"
          :data-image-id="image.id"
          class="col-lg-4 col-md-6 col-sm-12 gallery-item-wrapper"
          :class="{ 'animate__animated animate__fadeInUp': image.isVisible }"
          :style="`animation-delay: ${0.1 + index * 0.1}s;`"
          :title="`ID: ${image.id}, Visible: ${image.isVisible}`"
        >
          <el-card shadow="hover" :body-style="{ padding: '0px' }" class="gallery-item-card h-100">
            <el-image
              :src="image.src"
              :alt="image.alt || 'Gallery image'"
              fit="cover"
              class="gallery-image"
              lazy
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

      <div v-if="!isLoading && displayedImages.length > 0" class="text-center mt-5">
        <el-button
          type="danger"
          size="large"
          round
          @click="goToGalleryPage"
          class="cta-button"
          :class="{ 'animate__animated animate__bounceIn': isSectionVisible }"
          :style="{ 'animation-delay': '1.0s' }"
        >
          Explore The Full Gallery
          <el-icon class="el-icon--right"><Right /></el-icon>
        </el-button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue';
import { useRouter } from 'vue-router';
import { Picture, Right } from '@element-plus/icons-vue';

const router = useRouter();

const props = defineProps({
  sectionTitle: {
    type: String,
    default: 'Our Photo Gallery'
  },
  sectionSubtitle: {
    type: String,
    default: 'Discover the elegant beauty and memorable moments at Hobitel.'
  },
  imagesToShow: {
    type: Number,
    default: 6 // Only show 6 images on the homepage
  }
});

const allImages = ref([]);
const isLoading = ref(true);

const gallerySectionRef = ref(null);
const isSectionVisible = ref(false);
let sectionObserver;

let imageObserver;

onMounted(() => {
  sectionObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          isSectionVisible.value = true;
          sectionObserver.unobserve(entry.target);
        }
      });
    },
    { threshold: 0.1 } // Lower the activation threshold to 0.1
  );

  if (gallerySectionRef.value) {
    sectionObserver.observe(gallerySectionRef.value);
  }

  loadImagesForSection();
});

onUnmounted(() => {
  if (sectionObserver) {
    sectionObserver.disconnect();
  }
  if (imageObserver) {
    imageObserver.disconnect();
  }
});

const processImageData = (images) => {
  if (!Array.isArray(images)) return [];
  return images.map((img, idx) => ({
    id: img.id || `img-${Date.now() + Math.random()}-${idx}`,
    src: img.src,
    alt: img.alt || 'Gallery Image',
    caption: img.caption || '',
    category: img.category || 'General',
    isVisible: false, // Add isVisible state for each image
  }));
};

const loadImagesForSection = async () => {
  isLoading.value = true;
  await new Promise(resolve => setTimeout(resolve, 800)); // Simulate loading

  allImages.value = processImageData([
    { id: 'img-1', src: 'https://images.unsplash.com/photo-1505826759037-406b40feb4cd?q=80&w=400&auto=format&fit=crop', alt: 'Luxury hotel poolside view', caption: 'Luxury Pool', category: 'Outdoor' },
    { id: 'img-2', src: 'https://images.unsplash.com/photo-1566073771259-6a8506099945?q=80&w=400&auto=format&fit=crop', alt: 'Modern hotel lobby', caption: 'Modern Lobby', category: 'Indoor' },
    { id: 'img-3', src: 'https://images.unsplash.com/photo-1520250497591-112f2f40a3f4?q=80&w=400&auto=format&fit=crop', alt: 'Breakfast by the pool', caption: 'Exquisite Breakfast', category: 'Dining' },
    { id: 'img-4', src: 'https://images.unsplash.com/photo-1582719508461-905c673771fd?q=80&w=400&auto=format&fit=crop', alt: 'Comfortable hotel room bed', caption: 'Comfortable Bedroom', category: 'Rooms' },
    { id: 'img-5', src: 'https://images.unsplash.com/photo-1561501900-3701fa6a0864?q=80&w=400&auto=format&fit=crop', alt: 'Hotel exterior with palm trees', caption: 'Unique Architecture', category: 'Outdoor' },
    { id: 'img-6', src: 'https://images.unsplash.com/photo-1590073242678-70ee3fc28e8e?q=80&w=400&auto=format&fit=crop', alt: 'Hotel restaurant dining area', caption: 'Premium Restaurant', category: 'Dining' },
  ]);
  isLoading.value = false;

  await nextTick();
  setupImageObserver();
};

const displayedImages = computed(() => {
  return allImages.value.slice(0, props.imagesToShow);
});

const goToGalleryPage = () => {
  router.push('/gallery');
};

const setupImageObserver = () => {
  if (imageObserver) {
    imageObserver.disconnect();
  }
  imageObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          const imageId = entry.target.dataset.imageId;
          const foundImage = displayedImages.value.find(img => img.id === imageId);
          if (foundImage) {
            foundImage.isVisible = true;
          }
          imageObserver.unobserve(entry.target);
        }
      });
    },
    { threshold: 0.1 }
  );

  document.querySelectorAll('.gallery-item-wrapper').forEach(el => {
    if (!el.classList.contains('animate__animated')) {
      imageObserver.observe(el);
    }
  });
};
</script>

<style scoped>
/* Scoped styles remain the same, only comments are translated */
.gallery-section {
  background-color: #ffffff;
  padding-top: 4rem;
  padding-bottom: 4rem;
  overflow: hidden;
}

/* Base styles for elements WITHOUT animate__animated (initial hidden state) */
.section-title,
.section-subtitle,
.title-underline-container,
.cta-button {
  opacity: 0;
  transform: translateY(20px);
}
.gallery-item-wrapper {
  opacity: 0;
  transform: translateY(20px);
}

/* When animate__animated class is present, animate.css handles the animation */
.animate__animated {
  opacity: 1;
  transform: translateY(0);
}

/* Title & Subtitle */
.section-title {
  color: #1A3760;
  font-family: 'Georgia', 'Times New Roman', Times, serif;
  font-size: 2.5rem;
  font-weight: bold;
}

.section-subtitle {
  font-size: 1.05rem;
  color: #6c757d;
  margin-bottom: 1.5rem;
  font-family: 'Roboto', sans-serif;
}

/* Underline */
.title-underline-container {
  display: flex;
  justify-content: center;
  margin-top: 0.5rem;
}

.title-underline {
  display: flex;
  height: 4px;
  width: 120px;
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

/* Gallery Item Card */
.gallery-item-card {
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.07);
  transition: transform 0.3s ease-in-out, box-shadow 0.3s ease-in-out;
  cursor: pointer;
}

.gallery-item-card:hover {
  transform: translateY(-5px) scale(1.01);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.12) !important;
}

.gallery-image {
  width: 100%;
  height: 200px; /* Reduced image height on desktop */
  display: block;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.gallery-item-card:hover .gallery-image {
    transform: scale(1.03);
}

.gallery-caption {
  background-color: #fcfcfc;
  border-top: 1px solid #f0f0f0;
  margin-top: auto;
  min-height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.75rem;
}
.gallery-caption span {
  color: #333;
  font-size: 0.9rem;
  font-weight: 500;
  font-family: 'Open Sans', sans-serif;
}

/* Image Slot (placeholder/error) */
.image-slot {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  min-height: 200px; /* Matches image height */
  background: #eef1f5;
  color: #a8b1c2;
  font-size: 14px;
}

.image-slot .el-icon {
  font-size: 25px;
  margin-bottom: 6px;
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

/* CTA Button */
.cta-button {
  font-weight: bold;
  letter-spacing: 0.5px;
  padding: 0.6rem 1.8rem;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.cta-button:hover {
    transform: translateY(-2px);
    box-shadow: 0 3px 10px rgba(217, 83, 79, 0.3);
}


/* Responsive Adjustments */
@media (max-width: 991.98px) {
  .section-title {
    font-size: 2.2rem;
  }
  .section-subtitle {
    font-size: 1rem;
  }
  .gallery-image {
    height: 180px;
  }
  .image-slot {
    min-height: 180px;
  }
}

@media (max-width: 767.98px) {
  .section-title {
    font-size: 1.8rem;
  }
  .section-subtitle {
    font-size: 0.9rem;
  }
  .title-underline {
    width: 80px;
  }
  .gallery-image {
    height: 150px;
  }
  .image-slot {
    min-height: 150px;
  }
  .gallery-item-card {
    margin-bottom: 1rem;
  }
  .cta-button {
    padding: 0.5rem 1.5rem;
    font-size: 0.9rem;
  }
}
</style>