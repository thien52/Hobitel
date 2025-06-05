<template>
  <div class="rooms-page">
    <!-- Hero Section for Rooms Page -->
    <section class="rooms-hero py-5 text-center bg-light">
      <div class="container">
        <h1 class="page-title">Our Exquisite Rooms & Suites</h1>
        <p class="lead text-muted">
          Discover comfort and luxury in every corner. Find the perfect room for your stay.
        </p>
      </div>
    </section>

    <section class="rooms-list-section py-5">
      <div class="container">
        <!-- Filters and Sort Controls -->
        <el-card class="mb-4 filter-sort-card" shadow="never">
          <div class="row g-3 align-items-center">
            <div class="col-md-5 col-lg-4">
              <el-select v-model="filterType" placeholder="Filter by Room Type" clearable style="width: 100%;">
                <el-option label="All Types" value="all" />
                <el-option
                  v-for="type in uniqueRoomTypes"
                  :key="type"
                  :label="type.charAt(0).toUpperCase() + type.slice(1)"
                  :value="type"
                />
              </el-select>
            </div>
            <div class="col-md-4 col-lg-3">
              <el-select v-model="sortBy" placeholder="Sort by" clearable style="width: 100%;">
                <el-option label="Default" value="" />
                <el-option label="Price: Low to High" value="price_asc" />
                <el-option label="Price: High to Low" value="price_desc" />
                <el-option label="Rating: High to Low" value="rating_desc" />
              </el-select>
            </div>
            <div class="col-md-3 col-lg-2">
                <el-button @click="resetFilters" plain style="width: 100%;">Reset Filters</el-button>
            </div>
            <div class="col-lg-3 text-lg-end mt-3 mt-lg-0">
              <span v-if="!isLoading && totalFilteredRooms > 0" class="text-muted">
                Showing {{ paginatedRooms.length }} of {{ totalFilteredRooms }} rooms
              </span>
            </div>
          </div>
        </el-card>

        <!-- Loading State -->
        <div v-if="isLoading" class="text-center py-5">
          <el-skeleton :rows="5" animated />
        </div>

        <!-- No Rooms Found State -->
        <div v-else-if="!isLoading && paginatedRooms.length === 0" class="text-center py-5">
          <el-empty description="No rooms match your criteria. Please try adjusting the filters.">
             <el-button type="primary" @click="resetFilters">Reset All Filters</el-button>
          </el-empty>
        </div>

        <!-- Rooms List -->
        <div v-else class="row">
          <div
            v-for="room in paginatedRooms"
            :key="room.id"
            class="col-lg-4 col-md-6 mb-4 d-flex align-items-stretch"
          >
            <!-- Phần Card phòng giống hệt RoomsSection.vue -->
            <el-card :body-style="{ padding: '0px' }" shadow="hover" class="room-card w-100">
              <div class="room-content-default">
                <div class="room-image-container">
                  <img :src="room.imageUrl" :alt="room.name" class="room-image" />
                </div>
                <div class="room-info-block">
                  <div class="d-flex justify-content-between align-items-center mb-2">
                    <span class="price-text">PRICE {{ room.price }} NIGHT</span>
                    <el-rate
                      :model-value="room.rating" 
                      disabled
                      size="small"
                      :colors="rateColors"
                      disabled-void-color="#E0E0E0"
                      class="room-rating"
                    />
                  </div>
                  <h5 class="room-name mb-0">{{ room.name }}</h5>
                </div>
              </div>
              <div class="room-hover-overlay">
                <div class="overlay-content p-4">
                  <div class="d-flex justify-content-between align-items-center mb-2">
                    <span class="price-text-overlay">PRICE {{ room.price }} NIGHT</span>
                    <el-rate
                      :model-value="room.rating" 
                      size="small"
                      :colors="rateColors"
                      disabled-void-color="#E0E0E0"
                      class="room-rating-overlay"
                    />
                  </div>
                  <h4 class="room-name-overlay mb-3">{{ room.name }}</h4>
                  <p class="room-description-overlay text-muted mb-4">
                    {{ room.description }}
                  </p>
                  <div class="d-flex justify-content-between">
                    <el-button type="danger" plain class="details-button" @click="viewRoomDetails(room.id)">DETAILS</el-button>
                    <el-button type="primary" class="book-now-button" @click="bookRoom(room.id)">BOOK NOW</el-button>
                  </div>
                </div>
              </div>
            </el-card>
            <!-- Hết Phần Card phòng -->
          </div>
        </div>

        <!-- Pagination -->
        <div v-if="!isLoading && totalFilteredRooms > roomsPerPage" class="mt-5 d-flex justify-content-center">
          <el-pagination
            background
            layout="prev, pager, next, jumper"
            :total="totalFilteredRooms"
            :page-size="roomsPerPage"
            :current-page="currentPage"
            @current-change="handlePageChange"
          />
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';
import { useRouter, useRoute } from 'vue-router'; // useRoute để đọc query params

const router = useRouter();
const route = useRoute(); // Để đọc query params
const rateColors = ['#FFC107', '#FFC107', '#FFC107'];

const isLoading = ref(true); // Thêm trạng thái loading

// Danh sách đầy đủ các phòng (có thể fetch từ API trong thực tế)
const allRooms = ref([
  { id: 1,
    imageUrl: 'https://images.unsplash.com/photo-1566073771259-6a8506099945?q=80&w=870&auto=format&fit=crop',
    price: '$120.00',
    name: 'Luxury Suite Room',
    rating: 5,
    description: 'Spacious suite with a king-size bed, separate living area, and panoramic city views. Includes premium amenities.',
    type: 'suite'},

  { id: 2,
    imageUrl: 'https://images.unsplash.com/photo-1590490360182-c33d57733427?q=80&w=774&auto=format&fit=crop', 
    price: '$150.00',
    name: 'Deluxe King Room', 
    rating: 4,
    description: 'Comfortable deluxe room with a plush king-size bed, modern decor, and a work desk. Ideal for business or leisure.',
    type: 'deluxe'
  },

  { id: 3,
    imageUrl: 'https://images.unsplash.com/photo-1596394516093-501ba68a0ba6?q=80&w=870&auto=format&fit=crop',
    price: '$99.00',
    name: 'Standard Double Room',
    rating: 5,
    description: 'A cozy room featuring two comfortable double beds, perfect for families or friends travelling together.',
    type: 'standard'
  },

  { id: 4, 
    imageUrl: 'https://images.unsplash.com/photo-1582719478250-c89cae4dc85b?q=80&w=870&auto=format&fit=crop',
    price: '$180.00', 
    name: 'Ocean View Suite',
    rating: 5,
    description: 'Breathtaking ocean views from your private balcony. This suite offers unparalleled luxury and relaxation.', 
    type: 'suite'
    },

  { id: 5, 
    imageUrl: 'https://images.unsplash.com/photo-1611892440504-42a792e24d32?q=80&w=870&auto=format&fit=crop',
    price: '$110.00', 
    name: 'Cozy Single Room', 
    rating: 4,
    description: 'Perfect for solo travelers, this room offers a comfortable single bed and all essential amenities.', 
    type: 'standard'
  },

  { id: 6, 
    imageUrl: 'https://hibtel.webhotel.vn/assets/image/rooms-1.jpg',
    price: '$220.00',
    name: 'Presidential Suite', 
    rating: 5, 
    description: 'The pinnacle of luxury, featuring multiple rooms, a dining area, and personalized butler service.', 
    type: 'suite'
  },

  { id: 7,
    imageUrl: 'https://images.unsplash.com/photo-1578683010236-d716f9a3f461?q=80&w=870&auto=format&fit=crop',
    price: '$130.00',
    name: 'Family Room',
    rating: 4,
    description: 'Spacious room with connecting doors, ideal for families with children. Includes fun amenities for kids.',
    type: 'family'
  },

  { id: 8, 
    imageUrl: 'https://images.unsplash.com/photo-1631049307264-da0ec9d70304?q=80&w=870&auto=format&fit=crop', 
    price: '$165.00',
    name: 'Executive Deluxe Room', 
    rating: 5,
    description: 'Enhanced deluxe room with access to the executive lounge and upgraded amenities for a productive stay.',
    type: 'deluxe'
  }
]);

// Filters and Sort state
const filterType = ref(route.query.type || 'all'); // Lấy từ query param nếu có
const sortBy = ref(route.query.sort || '');

// Pagination state
const currentPage = ref(parseInt(route.query.page) || 1);
const roomsPerPage = ref(6);

// Lấy các loại phòng duy nhất để hiển thị trong filter select
const uniqueRoomTypes = computed(() => {
  const types = new Set(allRooms.value.map(room => room.type));
  return Array.from(types).sort();
});

// Computed property cho các phòng đã được filter và sort
const filteredAndSortedRooms = computed(() => {
  let roomsToProcess = [...allRooms.value];

  // Apply Filter by Type
  if (filterType.value && filterType.value !== 'all') {
    roomsToProcess = roomsToProcess.filter(room => room.type === filterType.value);
  }

  // Apply Sort
  if (sortBy.value) {
    roomsToProcess.sort((a, b) => {
      if (sortBy.value === 'price_asc') {
        return parseFloat(a.price.slice(1)) - parseFloat(b.price.slice(1));
      }
      if (sortBy.value === 'price_desc') {
        return parseFloat(b.price.slice(1)) - parseFloat(a.price.slice(1));
      }
      if (sortBy.value === 'rating_desc') {
        return b.rating - a.rating;
      }
      return 0;
    });
  }
  return roomsToProcess;
});

// Tổng số phòng sau khi filter (dùng cho pagination)
const totalFilteredRooms = computed(() => filteredAndSortedRooms.value.length);

// Computed property cho các phòng hiển thị trên trang hiện tại (đã phân trang)
const paginatedRooms = computed(() => {
  if (isLoading.value) return []; // Trả về mảng rỗng nếu đang load
  const start = (currentPage.value - 1) * roomsPerPage.value;
  const end = start + roomsPerPage.value;
  return filteredAndSortedRooms.value.slice(start, end);
});

// Hàm điều hướng và xử lý logic
const viewRoomDetails = (roomId) => {
  router.push(`/rooms/${roomId}`);
};

const bookRoom = (roomId) => {
  // Có thể lưu các filter hiện tại vào query params trước khi chuyển trang
  updateQueryParams();
  router.push(`/booking/${roomId}`);
};

const resetFilters = () => {
  filterType.value = 'all';
  sortBy.value = '';
  currentPage.value = 1; // Reset về trang đầu
  updateQueryParams();
};

const handlePageChange = (newPage) => {
  currentPage.value = newPage;
  window.scrollTo({ top: document.querySelector('.rooms-list-section').offsetTop - 80, behavior: 'smooth' });
  updateQueryParams();
};

// Hàm cập nhật query params trên URL
const updateQueryParams = () => {
  const query = {};
  if (filterType.value && filterType.value !== 'all') query.type = filterType.value;
  if (sortBy.value) query.sort = sortBy.value;
  if (currentPage.value > 1) query.page = currentPage.value;

  router.push({ query }); // Chỉ cập nhật query, không reload trang
};

// Watchers để tự động cập nhật URL khi filter/sort thay đổi và reset về trang 1
watch([filterType, sortBy], () => {
  currentPage.value = 1; // Khi filter hoặc sort thay đổi, quay về trang 1
  updateQueryParams();
});


onMounted(() => {
  // Giả lập fetching data
  setTimeout(() => {
    isLoading.value = false;
    // Nếu có query params lúc vào trang, áp dụng chúng
    if (route.query.type) filterType.value = route.query.type;
    if (route.query.sort) sortBy.value = route.query.sort;
    if (route.query.page) currentPage.value = parseInt(route.query.page);

  }, 700); // Thời gian giả lập load

  window.scrollTo(0, 0);
});
</script>

<style scoped>
/* Các style hiện tại của bạn rất tốt, giữ nguyên chúng */
.rooms-page { background-color: #fdfdfd; }
.rooms-hero { background-color: #e9ecef; border-bottom: 1px solid #dee2e6; }
.page-title { font-family: 'Georgia', serif; font-size: 2.8rem; font-weight: bold; color: #333; margin-bottom: 0.5rem; }
/* .rooms-list-section { background-color: #f8f9fa; } */ /* Có thể bỏ nếu muốn nền trắng đồng nhất */

.filter-sort-card {
  border: 1px solid #eee !important;
  border-radius: 8px;
  padding: 15px; /* Giảm padding một chút */
}

.room-card {
  border-radius: 15px !important;
  overflow: hidden;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  position: relative;
  background-color: #fff;
}
.room-image-container { 
  width: 100%; 
  aspect-ratio: 16 / 10; 
  overflow: hidden;
  background-color: #e0e0e0;
 }

.room-image { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
  display: block; 
  transition: transform 0.4s ease; 
}

.room-card:hover .room-image { 
  transform: scale(1.05); 
}

.room-info-block { 
  background-color: #ffffff; 
  padding: 20px; margin: 0 20px 20px 20px; 
  border-radius: 10px; 
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.08); 
  position: relative; 
  transform: translateY(-50px); 
  z-index: 1; 
  transition: opacity 0.3s ease, 
  transform 0.3s ease-out; 
}

.room-card:hover .room-info-block { 
  opacity: 0; 
  transform: translateY(-30px) scale(0.95); 
}

.price-text { 
  font-size: 0.7rem; 
  color: #777; 
  font-weight: 700; 
  text-transform: uppercase; 
  letter-spacing: 0.5px; 
}

.room-name { 
  font-family: 'Georgia', serif; 
  font-size: 1.3rem; 
  color: #333; 
  font-weight: bold; 
}

.room-rating :deep(.el-rate__icon) { 
  font-size: 16px; 
}

.room-hover-overlay { 
  position: absolute; 
  top: 0; 
  left: 0; 
  right: 0; 
  bottom: 0; 
  background-color: rgba(255, 255, 255, 0.98); 
  opacity: 0; visibility: hidden; 
  transition: opacity 0.4s ease, visibility 0s linear 0.4s; 
  z-index: 2; display: flex; 
  flex-direction: column; 
  justify-content: center; 
  border-radius: 15px; 
  padding: 25px; 
}

.room-card:hover .room-hover-overlay { 
  opacity: 1; 
  visibility: visible; 
  transition-delay: 0.05s; 
}

.overlay-content { 
width: 100%; 
text-align: left; 
}

.price-text-overlay { 
  font-size: 0.75rem; 
  color: #6c757d; 
  font-weight: 600; 
  text-transform: uppercase; 
}

.room-name-overlay { 
  font-family: 'Georgia', serif; 
  font-size: 1.6rem; 
  font-weight: 700; 
  color: #1A253C; 
  margin-top: 5px; 
}

.room-description-overlay { 
  font-size: 0.88rem; 
  line-height: 1.65; 
  color: #555; 
  min-height: 70px; 
  margin-bottom: 25px !important; 
}

.details-button, .book-now-button { 
  padding: 12px 22px !important; 
  font-weight: bold !important; 
  border-radius: 6px !important; 
  text-transform: uppercase; 
  font-size: 0.75rem !important; 
  letter-spacing: 0.5px; 
}

.book-now-button { 
  background-color: #C09153 !important; 
  border-color: #C09153 !important; 
  color: #fff !important; 
}

.book-now-button:hover { 
  background-color: #b38246 !important; 
  border-color: #b38246 !important; 
}

.details-button.el-button--danger.is-plain { 
  color: #C09153 !important; 
  background: transparent !important; 
  border-color: #C09153 !important; 
}

.details-button.el-button--danger.is-plain:hover { 
  background: #C09153 !important; 
  border-color: #C09153 !important; 
  color: #fff !important; 
}

.room-rating-overlay :deep(.el-rate__icon) { 
  margin-right: 2px; 
  font-size: 18px; 
}

.el-select, .el-button { /* Đảm bảo select và button có chiều cao phù hợp */
  height: 40px;
}

.el-pagination { 
  justify-content: center; }

/* Style cho el-empty khi không có phòng */
.el-empty {
  padding: 40px 0;
}
</style>