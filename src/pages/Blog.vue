<template>
  <div class="blog-page">
    <section class="blog-hero text-center py-5">
      <div class="container">
        <h1 class="page-title">Our Latest News & Articles</h1>
        <p class="lead text-muted page-subtitle">
          Stay updated with the latest happenings, tips, and stories from our
          team.
        </p>
      </div>
    </section>

    <section class="blog-content-section py-5">
      <div class="container">
        <div class="row">
          <div class="col-lg-8">
            <div v-if="filteredPosts.length === 0" class="text-center py-5">
              <el-empty
                :image-size="200"
                description="No blog posts found matching your criteria. Try adjusting your search or filters."
              >
                <el-button type="primary" @click="resetFiltersAndSearch">
                  <el-icon class="me-1"><Refresh /></el-icon>
                  Show All Posts
                </el-button>
              </el-empty>
            </div>
            <div v-else>
              <article
                v-for="post in filteredPosts"
                :key="post.id"
                class="blog-post-item mb-5"
              >
                <el-card shadow="hover" :body-style="{ padding: '0px' }">
                  <div class="row g-0">
                    <div class="col-md-5">
                      <img
                        :src="post.imageUrl"
                        :alt="post.title"
                        class="blog-post-image img-fluid"
                      />
                    </div>
                    <div class="col-md-7 d-flex flex-column">
                      <div class="card-body-content flex-grow-1">
                        <div class="post-meta mb-2">
                          <span
                            v-if="post.category"
                            class="post-category-tag me-3"
                          >
                            {{ post.category }}
                          </span>
                          <span class="post-date">{{ post.date }}</span>
                        </div>
                        <h3 class="post-title h4">
                          <a
                            href="#"
                            @click.prevent="viewPost(post.slug)"
                            class="text-decoration-none stretched-link blog-title-link"
                            >{{ post.title }}</a
                          >
                        </h3>
                        <p class="post-excerpt">
                          {{ post.excerpt }}
                        </p>
                      </div>
                      <div class="card-footer-action bg-transparent">
                        <a
                          href="#"
                          @click.prevent="viewPost(post.slug)"
                          class="btn btn-sm btn-brand-primary read-more-btn"
                          >Read More <i class="fas fa-arrow-right ms-1"></i
                        ></a>
                      </div>
                    </div>
                  </div>
                </el-card>
              </article>
            </div>
          </div>

          <aside class="col-lg-4">
            <div class="sidebar-widget">
              <h4 class="widget-title h5">Search Articles</h4>
              <div class="input-group">
                <input
                  type="text"
                  class="form-control"
                  placeholder="Search by title or excerpt..."
                  v-model="searchTerm"
                  @keyup.enter="applyFilters"
                />
                <button
                  class="btn btn-brand-primary"
                  type="button"
                  @click="applyFilters"
                >
                  <i class="fas fa-search"></i>
                </button>
              </div>
            </div>

            <div class="sidebar-widget">
              <h4 class="widget-title h5">Categories</h4>
              <ul class="list-unstyled widget-list">
                <li>
                  <a
                    href="#"
                    @click.prevent="resetCategoryFilter"
                    :class="[
                      'text-decoration-none category-link',
                      { active: !selectedCategorySlug },
                    ]"
                  >
                    All Categories
                    <span class="badge count-badge float-end">{{
                      allPosts.length
                    }}</span>
                  </a>
                </li>
                <li v-for="category in categories" :key="category.slug">
                  <a
                    href="#"
                    @click.prevent="filterByCategory(category.slug)"
                    :class="[
                      'text-decoration-none category-link',
                      { active: selectedCategorySlug === category.slug },
                    ]"
                  >
                    {{ category.name }}
                    <span class="badge count-badge float-end">{{
                      category.count
                    }}</span>
                  </a>
                </li>
              </ul>
            </div>

            <div class="sidebar-widget">
              <h4 class="widget-title h5">Recent Posts</h4>
              <ul class="list-unstyled widget-list">
                <li v-for="recentPost in recentPosts" :key="recentPost.id">
                  <a
                    href="#"
                    @click.prevent="viewPost(recentPost.slug)"
                    class="text-decoration-none recent-post-link"
                    >{{ recentPost.title }}</a
                  >
                </li>
              </ul>
            </div>
          </aside>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from "vue";
import { useRouter } from "vue-router";
import { Refresh } from "@element-plus/icons-vue"; // Import Refresh icon for the "Show All Posts" button
import axios from "@/axios";
const router = useRouter();

// Dữ liệu blog mẫu (đã được làm giàu thêm)
const allPosts = ref([]);
const fetchPosts = async () => {
  try {
    const response = await axios.get("/posts");
    allPosts.value = response.data;
    console.log(" Post nhận được:", response.data); // Thêm dòng này
  } catch (error) {
    console.error("Lỗi tải dữ liệu posts:", error);
  }
};
// ref để lưu trữ bài viết đã được lọc/tìm kiếm
const filteredPosts = ref([]);
const searchTerm = ref("");
const selectedCategorySlug = ref(null); // Lưu trữ slug của danh mục đang được chọn

// Computed properties
const categories = computed(() => {
  const cats = {};
  allPosts.value.forEach((post) => {
    if (post.category) {
      const slug = post.category.toLowerCase().replace(/\s+/g, "-");
      if (cats[slug]) {
        cats[slug].count++;
      } else {
        cats[slug] = { name: post.category, slug: slug, count: 1 };
      }
    }
  });
  // Sort categories alphabetically by name
  return Object.values(cats).sort((a, b) => a.name.localeCompare(b.name));
});

const recentPosts = computed(() => {
  // Sắp xếp theo ngày (mới nhất trước) hoặc ID giảm dần nếu ngày không có dạng chuẩn
  return [...allPosts.value]
    .sort((a, b) => new Date(b.date) - new Date(a.date))
    .slice(0, 4);
});

// Functions
const applyFilters = () => {
  let tempPosts = [...allPosts.value];

  // Apply category filter first
  if (selectedCategorySlug.value) {
    tempPosts = tempPosts.filter(
      (post) =>
        post.category &&
        post.category.toLowerCase().replace(/\s+/g, "-") ===
          selectedCategorySlug.value
    );
  }

  // Apply search term filter
  if (searchTerm.value.trim()) {
    const lowerSearchTerm = searchTerm.value.toLowerCase();
    tempPosts = tempPosts.filter(
      (post) =>
        post.title.toLowerCase().includes(lowerSearchTerm) ||
        post.excerpt.toLowerCase().includes(lowerSearchTerm) ||
        (post.content && post.content.toLowerCase().includes(lowerSearchTerm)) // Search in full content too
    );
  }

  filteredPosts.value = tempPosts;
};

const viewPost = async (slug) => {
  try {
    await router.push(`/blog/${slug}`);
  } catch (e) {
    console.error("Navigation error:", e);
  }
};

const filterByCategory = (slug) => {
  selectedCategorySlug.value = slug;
  searchTerm.value = ""; // Clear search when filtering by category
  applyFilters();
  // Scroll to content section for better UX after filtering
  const contentSection = document.querySelector(".blog-content-section");
  if (contentSection) {
    window.scrollTo({
      top:
        contentSection.offsetTop -
        (document.querySelector(".navbar")?.offsetHeight || 80),
      behavior: "smooth",
    });
  }
};

const resetCategoryFilter = () => {
  selectedCategorySlug.value = null; // Clear selected category
  applyFilters(); // Re-apply filters (which will now show all categories)
};

const resetFiltersAndSearch = () => {
  searchTerm.value = "";
  selectedCategorySlug.value = null;
  applyFilters(); // This will reset filteredPosts to allPosts
};

// Watch for changes in searchTerm or selectedCategorySlug to re-apply filters
watch([searchTerm, selectedCategorySlug], () => {
  applyFilters();
});

// Initial load: apply filters (this will show all posts initially)
onMounted(() => {
  fetchPosts();
  resetFiltersAndSearch(); // Ensure all posts are shown on initial load
  window.scrollTo(0, 0); // Scroll to top
});
</script>

<style scoped>
/* --- BIẾN MÀU VÀ FONT --- */
/* Cần định nghĩa các biến màu chính của theme nếu chưa có ở global CSS */
:root {
  --brand-primary-color: #c09153; /* Màu vàng cam */
  --brand-primary-hover-color: #a67c45; /* Hover của màu vàng cam */
  --brand-heading-color: #1a3760; /* Màu xanh đậm cho tiêu đề */
  --brand-text-color: #495057; /* Màu chữ chính */
  --brand-text-muted-color: #6c757d; /* Màu chữ phụ */
  --brand-light-bg: #f8f9fa; /* Nền sáng */
  --brand-white-bg: #ffffff; /* Nền trắng */
  --brand-border-color: #e9ecef; /* Màu viền */

  --font-family-heading: "Playfair Display", serif; /* Font cho tiêu đề */
  --font-family-body: "Roboto", sans-serif; /* Font cho nội dung */
}

/* --- STYLE CHUNG CHO TRANG --- */
.blog-page {
  background-color: var(--brand-light-bg);
  font-family: var(--font-family-body);
  color: var(--brand-text-color);
}

/* --- HERO SECTION --- */
.blog-hero {
  background: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)),
    url("https://images.unsplash.com/photo-1542435503-956c469947f6?q=80&w=1770&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D")
      no-repeat center center; /* Thay ảnh background */
  background-size: cover;
  color: #fff; /* Chữ trắng trên nền tối */
  padding-top: 6rem; /* Tăng padding để tạo khoảng trống */
  padding-bottom: 6rem;
  border-bottom: none; /* Không cần border-bottom */
}
.page-title {
  font-family: var(--font-family-heading);
  font-size: 3.5rem; /* Tăng kích thước tiêu đề */
  font-weight: 700;
  color: #fff; /* Màu trắng */
  margin-bottom: 1rem;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5); /* Thêm đổ bóng cho chữ */
}
.page-subtitle {
  color: rgba(255, 255, 255, 0.85); /* Màu trắng mờ */
  font-size: 1.25rem;
  max-width: 700px;
  margin-left: auto;
  margin-right: auto;
}

/* --- CONTENT SECTION --- */
.blog-content-section {
  padding-top: 3.5rem;
  padding-bottom: 3.5rem;
}

/* --- CARD BÀI VIẾT --- */
.blog-post-item .el-card {
  border-radius: 8px;
  border: 1px solid var(--brand-border-color);
  background-color: var(--brand-white-bg);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  overflow: hidden;
}
.blog-post-item .el-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 12px 30px rgba(26, 55, 96, 0.15) !important; /* Đổ bóng chuyên nghiệp hơn */
}

.blog-post-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  min-height: 280px;
}
@media (max-width: 767.98px) {
  .blog-post-image {
    height: 230px;
    border-top-left-radius: calc(8px - 1px);
    border-top-right-radius: calc(8px - 1px);
    border-bottom-left-radius: 0; /* Remove for mobile */
  }
}
@media (min-width: 768px) {
  .blog-post-image {
    border-top-left-radius: calc(8px - 1px);
    border-bottom-left-radius: calc(8px - 1px);
    border-top-right-radius: 0; /* Remove for desktop */
  }
}

.card-body-content {
  padding: 1.75rem;
  position: relative;
}

.post-meta {
  margin-bottom: 0.6rem !important;
}
.post-category-tag {
  font-size: 0.75rem; /* Tăng kích thước chữ tag */
  font-weight: 700;
  text-transform: uppercase;
  color: var(--brand-primary-color);
  letter-spacing: 0.8px;
  padding: 0.2rem 0.5rem;
  background-color: rgba(192, 145, 83, 0.1); /* Background nhẹ nhàng */
  border-radius: 4px;
}
.post-date {
  font-size: 0.85rem; /* Tăng kích thước chữ date */
  color: var(--brand-text-muted-color);
  text-transform: uppercase;
}

.post-title.h4 {
  font-family: var(--font-family-heading);
  font-size: 1.65rem;
  font-weight: 600;
  color: var(--brand-heading-color);
  margin-bottom: 0.75rem !important;
  line-height: 1.3;
}
.post-title a.blog-title-link {
  color: var(--brand-heading-color);
  transition: color 0.2s ease;
}
.post-title a.blog-title-link:hover {
  color: var(--brand-primary-color);
}

.post-excerpt {
  font-size: 0.95rem;
  line-height: 1.65;
  color: var(--brand-text-color);
  margin-bottom: 1.5rem;
}

.card-footer-action {
  padding: 0 1.75rem 1.75rem 1.75rem !important;
}
.btn-brand-primary {
  background-color: var(--brand-primary-color) !important;
  border-color: var(--brand-primary-color) !important;
  color: #fff !important;
  font-family: var(--font-family-body);
  font-weight: 600;
  font-size: 0.85rem; /* Tăng kích thước chữ button */
  text-transform: uppercase;
  padding: 0.7rem 1.4rem; /* Tăng padding button */
  border-radius: 5px;
  letter-spacing: 0.5px;
  transition: background-color 0.2s ease, border-color 0.2s ease,
    transform 0.2s ease;
}
.btn-brand-primary:hover {
  background-color: var(--brand-primary-hover-color) !important;
  border-color: var(--brand-primary-hover-color) !important;
  transform: translateY(-2px); /* Thêm hiệu ứng hover */
}
.read-more-btn i {
  transition: transform 0.2s ease-in-out;
  font-size: 0.7rem;
}
.read-more-btn:hover i {
  transform: translateX(4px);
}

/* --- SIDEBAR --- */
.sidebar-widget {
  background-color: var(--brand-white-bg);
  padding: 1.75rem !important;
  border-radius: 8px;
  border: 1px solid var(--brand-border-color);
  margin-bottom: 2rem !important;
}
.widget-title.h5 {
  font-family: var(--font-family-heading);
  color: var(--brand-heading-color);
  font-size: 1.35rem; /* Tăng kích thước tiêu đề widget */
  font-weight: 600;
  margin-bottom: 1rem !important;
  padding-bottom: 0.75rem;
  position: relative;
  border-bottom: 1px solid var(--brand-border-color);
}
.widget-title.h5::after {
  content: "";
  position: absolute;
  bottom: -1px;
  left: 0;
  width: 50px;
  height: 2px;
  background-color: var(--brand-primary-color);
}

.sidebar-widget .form-control {
  font-family: var(--font-family-body);
  border-right: none;
  font-size: 0.95rem; /* Tăng kích thước chữ input */
  height: calc(2.25rem + 2px); /* Fix chiều cao cho input */
}
.sidebar-widget .form-control:focus {
  border-color: var(--brand-primary-color);
  box-shadow: 0 0 0 0.2rem rgba(192, 145, 83, 0.25); /* Shadow với màu chính */
}
.sidebar-widget .input-group .btn-brand-primary {
  border-top-left-radius: 0;
  border-bottom-left-radius: 0;
}
.sidebar-widget .input-group .btn-brand-primary i {
  font-size: 1rem; /* Tăng kích thước icon search */
}

.widget-list {
  padding-left: 0;
}
.widget-list li {
  margin-bottom: 0.6rem !important;
}
.widget-list li a.category-link,
.widget-list li a.recent-post-link {
  color: var(--brand-text-color);
  transition: color 0.2s ease;
  display: block;
  padding: 0.3rem 0; /* Tăng padding item list */
  font-size: 0.95rem;
}
.widget-list li a.category-link:hover,
.widget-list li a.recent-post-link:hover {
  color: var(--brand-primary-color);
}
.widget-list li a.category-link.active {
  /* Style cho danh mục đang active */
  color: var(--brand-primary-color);
  font-weight: bold;
}
.count-badge.badge {
  font-size: 0.8rem; /* Tăng kích thước badge */
  background-color: var(--brand-light-bg) !important;
  color: var(--brand-text-muted-color) !important;
  padding: 0.3em 0.6em;
  font-weight: 500;
  border-radius: 4px;
}

/* Responsive adjustments */
@media (max-width: 991.98px) {
  /* Medium devices and down */
  .page-title {
    font-size: 2.8rem;
  }
  .page-subtitle {
    font-size: 1.1rem;
  }
}

@media (max-width: 767.98px) {
  /* Small devices (sm) and down */
  .blog-hero {
    padding-top: 4rem;
    padding-bottom: 4rem;
  }
  .page-title {
    font-size: 2.2rem;
  }
  .page-subtitle {
    font-size: 1rem;
  }
  .blog-content-section {
    padding-top: 2.5rem;
    padding-bottom: 2.5rem;
  }
  .blog-post-item .el-card {
    margin-bottom: 2.5rem !important; /* Adjust spacing on mobile */
  }
  .blog-post-image {
    border-radius: 8px 8px 0 0; /* For mobile, top corners rounded */
  }
  .card-body-content {
    padding: 1.25rem !important; /* Smaller padding on mobile */
  }
  .post-title.h4 {
    font-size: 1.4rem;
  }
  .post-excerpt {
    font-size: 0.875rem;
  }
  .card-footer-action {
    padding: 0 1.25rem 1.25rem 1.25rem !important; /* Smaller padding on mobile */
  }
  .btn-brand-primary {
    padding: 0.6rem 1rem;
    font-size: 0.75rem;
  }
  .sidebar-widget {
    padding: 1.5rem !important;
    margin-bottom: 1.5rem !important;
  }
  .widget-title.h5 {
    font-size: 1.15rem;
  }
}
</style>
