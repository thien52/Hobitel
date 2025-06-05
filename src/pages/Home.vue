<template>
  <div class="home">
    <div class="container py-4">
      <el-card class="card-check mb-5" shadow="always">
        <el-form :model="formInline" @submit.prevent="performCheckAvailability">
          <div class="row g-3 align-items-end">
            <div class="col-12 col-md-6 col-lg">
              <el-form-item label="Check in" class="w-100 mb-0">
                <el-date-picker
                  v-model="formInline.datein"
                  type="date"
                  placeholder="Chọn ngày nhận phòng"
                  clearable
                  style="width: 100%;"
                  :disabled-date="disabledDateIn"
                />
              </el-form-item>
            </div>

            <div class="col-12 col-md-6 col-lg">
              <el-form-item label="Check out" class="w-100 mb-0">
                <el-date-picker
                  v-model="formInline.dateout"
                  type="date"
                  placeholder="Chọn ngày trả phòng"
                  clearable
                  style="width: 100%;"
                  :disabled-date="disabledDateOut"
                />
              </el-form-item>
            </div>

            <div class="col-12 col-sm-6 col-md-4 col-lg">
              <el-form-item label="Adults" class="w-100 mb-0">
                <el-select
                  v-model="formInline.adults"
                  placeholder="Số người lớn"
                  clearable
                  style="width: 100%;"
                >
                  <!-- Giữ nguyên hoặc dùng v-for nếu danh sách động -->
                  <el-option v-for="n in 6" :key="`adult-${n}`" :label="`${n}`" :value="n" />
                </el-select>
              </el-form-item>
            </div>

            <div class="col-12 col-sm-6 col-md-4 col-lg">
              <el-form-item label="Child" class="w-100 mb-0">
                <el-select
                  v-model="formInline.child"
                  placeholder="Số trẻ em"
                  clearable
                  style="width: 100%;"
                >
                  <el-option label="0" :value="0" /> <!-- Thêm lựa chọn 0 trẻ em -->
                  <el-option v-for="n in 5" :key="`child-${n}`" :label="`${n}`" :value="n" />
                </el-select>
              </el-form-item>
            </div>

            <div class="col-12 col-md-4 col-lg-auto">
              <el-form-item class="w-100 mb-0">
                <!-- 
                  - :disabled="isChecking" để vô hiệu hóa nút khi đang xử lý
                  - type="submit" để rõ ràng hơn về vai trò của nút trong form
                -->
                <el-button
                  type="primary"
                  native-type="submit"
                  class="w-100 custom-button"
                  :loading="isChecking"
                  :disabled="isChecking"
                >
                  <!-- {{ isChecking ? 'ĐANG KIỂM TRA...' : 'KIỂM TRA PHÒNG TRỐNG' }} -->
                  <span v-if="isChecking">Loading...</span>
                  <span v-else>Checking</span>
                </el-button>
              </el-form-item>
            </div>
          </div>
        </el-form>

        <!-- Hiển thị thông báo kết quả -->
        <div v-if="availabilityResult" class="mt-3">
          <el-alert
            :title="availabilityResult.title"
            :type="availabilityResult.type"
            :description="availabilityResult.message"
            show-icon
            closable
            @close="availabilityResult = null"
          />
        </div>
      </el-card>

      <!-- Section cho About -->
      <section id="about" class="about-section mb-5">
        <AboutSection />
      </section>

      <!-- Section cho Rooms -->
      <section id="rooms" class="rooms-section mb-5">
        <RoomsSection :filter-params="searchParamsForRooms" />
      </section>

      <section id="contact" class="contact-section mb-5">
        <Contact />
      </section>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'
import AboutSection from "../components/AboutSection.vue";
import RoomsSection from "../components/RoomsSection.vue";
import Contact from './Contact.vue'; // Giả sử Contact.vue là một page component

const formInline = reactive({
  datein: '',
  dateout: '',
  adults: 1, // Giá trị mặc định
  child: 0,  // Giá trị mặc định
})

const isChecking = ref(false) // Trạng thái đang kiểm tra
const availabilityResult = ref(null) // { type: 'success'/'error', title: '...', message: '...' }
const searchParamsForRooms = ref(null) // Dùng để truyền xuống RoomsSection sau khi check

// Logic vô hiệu hóa ngày cho date picker
const disabledDateIn = (time) => {
  // Không cho chọn ngày trong quá khứ
  return time.getTime() < Date.now() - 8.64e7; // trừ 1 ngày để ngày hiện tại vẫn chọn được
}

const disabledDateOut = (time) => {
  // Không cho chọn ngày trước ngày check-in hoặc ngày trong quá khứ
  if (formInline.datein) {
    return time.getTime() < new Date(formInline.datein).getTime();
  }
  return time.getTime() < Date.now() - 8.64e7;
}

const performCheckAvailability = async () => {
  if (!formInline.datein || !formInline.dateout) {
    ElMessage.error('Vui lòng chọn ngày nhận và trả phòng.');
    return;
  }
  if (new Date(formInline.dateout) <= new Date(formInline.datein)) {
    ElMessage.error('Ngày trả phòng phải sau ngày nhận phòng.');
    return;
  }

  isChecking.value = true
  availabilityResult.value = null // Xóa kết quả cũ
  console.log('Đang kiểm tra phòng trống với:', formInline)

  // --- Giả lập gọi API ---
  await new Promise(resolve => setTimeout(resolve, 1500));
  // --- Kết thúc giả lập ---

  // Logic xử lý kết quả (ví dụ)
  const isAvailable = Math.random() > 0.3; // Giả sử 70% là có phòng

  if (isAvailable) {
    availabilityResult.value = {
      type: 'success',
      title: 'Có phòng trống!',
      message: `Tìm thấy các lựa chọn phù hợp từ ${formInline.datein} đến ${formInline.dateout} cho ${formInline.adults} người lớn và ${formInline.child} trẻ em.`
    }
    // Cập nhật params để RoomsSection có thể lọc (nếu cần)
    searchParamsForRooms.value = { ...formInline };
     // Tự động cuộn xuống section phòng
    const roomsSectionElement = document.getElementById('rooms');
    if (roomsSectionElement) {
      roomsSectionElement.scrollIntoView({ behavior: 'smooth' });
    }

  } else {
    availabilityResult.value = {
      type: 'error',
      title: 'Không tìm thấy phòng trống',
      message: 'Rất tiếc, không có phòng nào phù hợp với lựa chọn của bạn. Vui lòng thử ngày khác hoặc liên hệ trực tiếp.'
    }
    searchParamsForRooms.value = null; // Reset filter nếu không có phòng
  }

  isChecking.value = false
}
</script>

<style scoped>
.custom-button {

  font-weight: bold;
}

.el-alert {
  border-radius: 4px;
}
</style>