<template>
  <div class="about-page-wrapper">
    <!-- Section 1: Welcome -->
    <AboutSection
      class="pb-0"
      image-url="https://images.unsplash.com/photo-1566073771259-6a8506099945?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
      image-alt="Hobitel Hotel Exterior"
      sub-heading="A WELCOME FROM HOBITEL"
      main-heading="Welcome to Hobitel: An Elevated Experience Awaits"
      description="Hobitel is more than just a hotel; it's a destination where you can find tranquility, luxury, and unforgettable moments. With our unique architecture, modern amenities, and dedicated service, we are committed to delivering a perfect stay that exceeds all expectations."
      promotional-message="Enjoy exclusive offers when booking directly through our website!"
      :promotion-icon="Discount"
      :feature1="{ icon: Star, title: 'Exceptional 5-Star Service', description: 'Our professional team is dedicated to serving you 24/7.' }"
      :feature2="{ icon: LocationFilled, title: 'Prime Location', description: 'In the heart of the city, close to major attractions.' }"
      :show-second-feature="true"
      cta-button-text="Explore Our Rooms"
      cta-button-link="/rooms"
    />

    <hr class="my-5 divider-line">

    <!-- Section 2: Vision & Mission -->
    <AboutSection
      class="pt-0"
      image-url="https://i.pinimg.com/736x/8b/11/98/8b1198d19636f8918cd433afb8cba1a4.jpg"
      image-alt="Hobitel Mission"
      sub-heading="OUR VISION & MISSION"
      main-heading="Our Mission: To Elevate the Guest Experience"
      description="At Hobitel, we believe every stay tells a story. Our mission is to craft these exceptional stories through a perfect blend of luxurious accommodations, world-class facilities, and personalized service. We continuously innovate to deliver the best possible value to our guests."
      :promotional-message="null" 
      :feature1="{ icon: TrendCharts, title: 'Constant Innovation', description: 'Always updating with the latest trends and technology to serve you better.' }"
      :feature2="{ icon: UserFilled, title: 'Guest-Centric Focus', description: 'Every decision is driven by your satisfaction.' }"
      :show-second-feature="true"
      cta-button-text="Meet Our Team"
      cta-button-link="/team" />

    </div>
</template>

<script setup>
import { ref } from 'vue';
import AboutSection from '../components/AboutSection.vue';

// Image data for the second section (can be used if needed)
const about2ImageUrl = ref('https://images.unsplash.com/photo-1549298244-0b16894c5011?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D');

// You can add other custom logic functions here if needed
// const customActionForSummerPromo = () => {
//   console.log('User clicked on the summer promo!');
//   // Example: Open a modal with offer details or navigate to a specific promotions page
//   // router.push('/promotions/summer-deal');
// };
</script>

<style scoped>
.about-page-wrapper {
  /* You can add top and bottom padding for the entire page */
  padding-top: 2rem;
  padding-bottom: 2rem;
}

.divider-line {
    border: 0;
    height: 1px;
    background-image: linear-gradient(to right, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.1), rgba(0, 0, 0, 0));
    width: 80%; /* Width of the divider line */
    margin-left: auto;
    margin-right: auto;
}
</style>