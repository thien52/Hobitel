<template>
  <div>
    <AboutSection
      main-heading="Chào mừng đến với Hobitel"
      description="Một nơi tuyệt vời để nghỉ dưỡng và khám phá..."
      :feature1="{ icon: Star, title: 'Dịch vụ 5 sao', description: 'Trải nghiệm dịch vụ đẳng cấp quốc tế.' }"
      :show-second-feature="false"
      cta-button-text="Khám Phá Phòng"
      cta-button-link="/rooms"
    />

    <hr>

    <AboutSection
      sub-heading="ƯU ĐÃI ĐẶC BIỆT"
      main-heading="Giảm Giá Mùa Hè"
      promotional-message="Đặt phòng ngay để nhận ưu đãi 30% cho kỳ nghỉ hè của bạn!"
      :promotion-icon="Ticket" 
      :on-cta-click="customActionForSummerPromo"
      cta-button-text="Xem Chi Tiết Ưu Đãi"
    />
  </div>
</template>

<script setup>
import AboutSection from '../components/AboutSection.vue';

const customActionForSummerPromo = () => {
  console.log('Người dùng click vào ưu đãi mùa hè!');
  // Có thể mở modal, điều hướng tới trang khác, hoặc thực hiện hành động bất kỳ
};
</script>
