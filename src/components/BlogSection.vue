<template>
  <section class="blog-section py-5">
    <div class="container">
      <h2 class="section-title text-center mb-5">
        Latest From Our Blog
        <p class="lead text-muted section-subtitle-text mt-2">Stay informed with our insights, news, and stories.</p>
        <div class="divider"></div>
      </h2>

      <!-- V-if để xử lý trường hợp không có bài viết nào -->
      <div v-if="latestPosts.length > 0" class="row justify-content-center">
        <div
          v-for="post in latestPosts"
          :key="post.id"
          class="col-lg-4 col-md-6 mb-4 d-flex align-items-stretch"
        >
          <!-- Giao diện card được thiết kế cho trang chủ -->
          <el-card shadow="hover" :body-style="{ padding: '0px' }" class="blog-card w-100">
            <img :src="post.imageUrl" :alt="post.title" class="blog-card-image" />
            <div class="card-content p-4">
              <div class="post-meta mb-2">
                <span v-if="post.category" class="post-category-tag me-2">
                  {{ post.category }}
                </span>
                <span class="post-date">{{ post.date }}</span>
              </div>
              <h4 class="post-title h5 mb-3">
                <a href="#" @click.prevent="viewPost(post.slug)" class="text-decoration-none blog-title-link stretched-link">
                  {{ post.title }}
                </a>
              </h4>
              <p class="post-excerpt text-muted mb-3">
                {{ post.excerpt }}
              </p>
              <div class="card-footer-action mt-auto">
                <a href="#" @click.prevent="viewPost(post.slug)" class="btn btn-sm btn-brand-primary read-more-btn">Read More <i class="fas fa-arrow-right ms-1"></i></a>
              </div>
            </div>
          </el-card>
        </div>
      </div>
      <div v-else class="text-center">
        <p>No recent posts to display.</p>
      </div>

      <div class="text-center mt-5 view-all-blogs-container">
        <el-button type="danger" size="large" round @click="goToBlogPage">
            VIEW ALL ARTICLES
            <el-icon class="el-icon--right"><Right /></el-icon>
        </el-button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useRouter } from 'vue-router';
import { Right } from '@element-plus/icons-vue';

const router = useRouter();

// 1. DỮ LIỆU ĐƯỢC ĐẶT TRỰC TIẾP BÊN TRONG COMPONENT
// Component này giờ đây hoàn toàn tự chủ.
const allPosts = ref([
  {
    id: 1, slug: 'exploring-modern-architecture', title: 'Exploring the Wonders of Modern Architecture in City X',
    imageUrl: 'https://images.unsplash.com/photo-1480074568708-e7b720bb3f09?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=874&q=80',
    date: 'October 26, 2023', category: 'Architecture',
    excerpt: 'Discover the breathtaking modern architectural marvels that City X has to offer. A journey through design and innovation that redefines urban landscapes.',
  },
  {
    id: 2, slug: 'gourmet-dining-experience', title: 'A Culinary Journey: The Best Gourmet Dining Spots',
    imageUrl: 'https://images.unsplash.com/photo-1414235077428-338989a2e8c0?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=870&q=80',
    date: 'October 22, 2023', category: 'Food & Dining',
    excerpt: 'Embark on a delightful culinary adventure as we explore the finest gourmet restaurants renowned for their exquisite dishes and ambiance.',
  },
  {
    id: 3, slug: 'wellness-retreats-for-relaxation', title: 'Top Wellness Retreats for Ultimate Relaxation',
    imageUrl: 'https://images.unsplash.com/photo-1544161515-4ab6ce6db874?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=870&q=80',
    date: 'October 18, 2023', category: 'Wellness',
    excerpt: 'Unwind and recharge at these top-rated wellness retreats, offering a perfect escape for peace of mind, body, and soul.',
  },
  {
    id: 4, slug: 'local-culture-and-festivals', title: 'Immersing in Local Culture: A Guide to Festivals',
    imageUrl: 'https://i.pinimg.com/736x/38/88/b4/3888b4278a0c94d56e88e6326dcb779e.jpg',
    date: 'October 15, 2023', category: 'Culture',
    excerpt: 'Experience the heart and soul of different cultures by participating in their most colorful and vibrant local festivals and traditions.',
  },
  // Thêm các bài viết khác nếu muốn
]);

// 2. LOGIC TÍNH TOÁN NỘI TẠI
// Tự động sắp xếp và chỉ lấy 3 bài viết mới nhất để hiển thị.
const latestPosts = computed(() => {
  return [...allPosts.value]
    .sort((a, b) => new Date(b.date) - new Date(a.date))
    .slice(0, 3);
});

const viewPost = (slug) => {
  router.push(`/blog/${slug}`);
};

const goToBlogPage = () => {
  router.push('/blog');
};
</script>

<style scoped>
/* 3. CSS ĐỒNG BỘ VỚI BLOGPAGE.VUE */
.blog-section {
  --brand-primary-color: #C09153;
  --brand-primary-hover-color: #a67c45;
  --brand-heading-color: #1A3760;
  --brand-text-color: #495057;
  --brand-text-muted-color: #6c757d;
  --brand-light-bg: #f8f9fa;
  --brand-white-bg: #ffffff;
  --font-family-heading: 'Playfair Display', serif;
  --font-family-body: 'Roboto', sans-serif;
  
  background-color: var(--brand-light-bg);
}

.section-title {
  font-family: var(--font-family-heading);
  font-size: 2.8rem;
  color: var(--brand-heading-color);
  font-weight: 700;
  position: relative;
  padding-bottom: 1rem;
}

.section-subtitle-text {
  font-family: var(--font-family-body);
  font-size: 1.15rem;
  color: var(--brand-text-muted-color);
}

.divider {
  width: 70px;
  height: 3px;
  background-color: var(--brand-primary-color);
  margin: 1.5rem auto 0;
  border-radius: 2px;
}

/* Giao diện card cho trang chủ (ảnh trên, text dưới) */
.blog-card {
  border-radius: 12px !important;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  background-color: var(--brand-white-bg);
  border: 1px solid #e9ecef;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.blog-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 10px 30px rgba(26, 55, 96, 0.15) !important;
}

.blog-card-image {
  width: 100%;
  height: 220px;
  object-fit: cover;
}

.card-content {
  padding: 1.5rem !important;
  display: flex;
  flex-direction: column;
  flex-grow: 1;
}

.post-meta {
  margin-bottom: 0.5rem !important;
}
.post-category-tag {
  font-size: 0.7rem;
  font-weight: 700;
  text-transform: uppercase;
  color: var(--brand-primary-color);
  letter-spacing: 0.8px;
  background-color: rgba(192, 145, 83, 0.1);
  padding: 0.2rem 0.5rem;
  border-radius: 4px;
}
.post-date {
  font-size: 0.75rem;
  color: var(--brand-text-muted-color);
  text-transform: uppercase;
}

.post-title.h5 {
  font-family: var(--font-family-heading);
  font-size: 1.4rem;
  font-weight: 600;
  color: var(--brand-heading-color);
  margin-bottom: 0.75rem !important;
  line-height: 1.3;
}
.blog-title-link {
  color: var(--brand-heading-color);
  transition: color 0.2s ease;
}
.blog-title-link:hover {
  color: var(--brand-primary-color);
}

.post-excerpt {
  font-size: 0.9rem;
  line-height: 1.6;
  color: var(--brand-text-color);
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  margin-bottom: 1.5rem;
}

.card-footer-action {
  padding-top: 1rem;
}

.btn-brand-primary {
  background-color: var(--brand-primary-color) !important;
  border-color: var(--brand-primary-color) !important;
  color: #fff !important;
  font-weight: 600;
  font-size: 0.85rem;
  text-transform: uppercase;
  padding: 0.7rem 1.4rem;
  border-radius: 5px;
  letter-spacing: 0.5px;
  transition: all 0.2s ease;
}
.btn-brand-primary:hover {
  background-color: var(--brand-primary-hover-color) !important;
  border-color: var(--brand-primary-hover-color) !important;
  transform: translateY(-2px);
}
.read-more-btn i {
  transition: transform 0.2s ease-in-out;
  font-size: 0.7rem;
}
.read-more-btn:hover i {
  transform: translateX(4px);
}

/* Nút VIEW ALL ARTICLES */
.view-all-blogs-container .el-button {
  background-color: var(--brand-primary-color) !important;
  border-color: var(--brand-primary-color) !important;
  color: #fff !important;
  font-weight: 600;
  padding: 0.8rem 2.2rem;
  font-size: 1.05rem;
  transition: all 0.3s ease;
}
.view-all-blogs-container .el-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(192, 145, 83, 0.4);
  background-color: var(--brand-primary-hover-color) !important;
  border-color: var(--brand-primary-hover-color) !important;
}
</style>