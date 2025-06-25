<template>
  <el-footer class="bg-dark text-white" style="padding: 40px 0; height: 100%">
    <div class="container">
      <el-row :gutter="20">
        <!-- Column 1: Logo and description -->
        <el-col :xs="24" :md="8">
          <h5 class="d-flex align-items-center">
            <img
              src="https://images.unsplash.com/photo-1588532242026-01df5951f6f5"
              alt="Hobitel Logo"
              width="30"
              height="30"
              class="me-2"
            />
            Hobitel
          </h5>
          <p>
            Welcome to Hobitel – where we provide the perfect vacation with
            world-class service and luxurious spaces.
          </p>
        </el-col>

        <!-- Column 2: Links -->
        <el-col :xs="24" :md="8">
          <h5 class="mb-3">Links</h5>
          <ul class="list-unstyled">
            <li>
              <a href="#" class="text-white text-decoration-none">Home</a>
            </li>
            <li>
              <a href="#" class="text-white text-decoration-none">About</a>
            </li>
            <li>
              <a href="#" class="text-white text-decoration-none">Rooms</a>
            </li>
          </ul>
        </el-col>

        <!-- Column 3: Contact -->
        <el-col :xs="24" :md="8">
          <h5 class="mb-3">Contact</h5>
          <p><i class="bi bi-geo-alt-fill"></i> 123 Beach Road, Da Nang City</p>
          <p><i class="bi bi-telephone-fill"></i> +84 123 456 789</p>
          <p><i class="bi bi-envelope-fill"></i> contact@hobitel.com</p>
        </el-col>
      </el-row>
    </div>
  </el-footer>
</template>

<script setup>
// No script is needed if you have Element Plus installed.
</script>

<style scoped>
.bg-dark {
  background-color: #212529;
}
.text-white {
  color: white;
}
.text-decoration-none {
  text-decoration: none;
}
.me-2 {
  margin-right: 0.5rem;
}
</style>
