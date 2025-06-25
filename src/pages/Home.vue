<template>
  <div class="home">
    <div class="container py-4">
      <!-- ROOM AVAILABILITY CHECKER FORM -->
      <el-card class="card-check mb-5" shadow="always">
        <el-form
          :model="formInline"
          :rules="rules"
          ref="formRef"
          @submit.prevent="performCheckAvailability"
        >
          <div class="row g-3 align-items-end">
            <!-- Check-in -->
            <div class="col-12 col-md-6 col-lg">
              <el-form-item
                label="Check-in"
                class="w-100 mb-0"
                prop="datein"
                required
              >
                <el-date-picker
                  v-model="formInline.datein"
                  type="date"
                  placeholder="Select check-in date"
                  clearable
                  style="width: 100%"
                  :disabled-date="disabledDateIn"
                />
              </el-form-item>
            </div>
            <!-- Check-out -->
            <div class="col-12 col-md-6 col-lg">
              <el-form-item
                label="Check-out"
                class="w-100 mb-0"
                prop="dateout"
                required
              >
                <el-date-picker
                  v-model="formInline.dateout"
                  type="date"
                  placeholder="Select check-out date"
                  clearable
                  style="width: 100%"
                  :disabled-date="disabledDateOut"
                />
              </el-form-item>
            </div>
            <!-- Adults -->
            <div class="col-12 col-sm-6 col-md-4 col-lg">
              <el-form-item
                label="Adults"
                class="w-100 mb-0"
                prop="adults"
                required
              >
                <el-select
                  v-model.number="formInline.adults"
                  placeholder="Number of adults"
                  clearable
                  style="width: 100%"
                >
                  <el-option
                    v-for="n in 6"
                    :key="`adult-${n}`"
                    :label="`${n}`"
                    :value="n"
                  />
                </el-select>
              </el-form-item>
            </div>
            <!-- Children -->
            <div class="col-12 col-sm-6 col-md-4 col-lg">
              <el-form-item label="Children" class="w-100 mb-0" prop="child">
                <el-select
                  v-model="formInline.child"
                  placeholder="Number of children"
                  clearable
                  style="width: 100%"
                >
                  <el-option label="0" :value="0" />
                  <el-option
                    v-for="n in 5"
                    :key="`child-${n}`"
                    :label="`${n}`"
                    :value="n"
                  />
                </el-select>
              </el-form-item>
            </div>
            <!-- Submit Button -->
            <div class="col-12 col-md-4 col-lg-auto">
              <el-form-item class="w-100 mb-0">
                <el-button
                  type="primary"
                  native-type="submit"
                  class="w-100 custom-button"
                  :loading="isChecking"
                  :disabled="isChecking"
                >
                  <span v-if="isChecking">Checking...</span>
                  <span v-else>Check Availability</span>
                </el-button>
              </el-form-item>
            </div>
          </div>
        </el-form>
        <!-- Availability Result -->
        <div v-if="availabilityResult" class="mt-4">
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

      <!-- PAGE SECTIONS -->
      <section id="about" class="about-section mb-5">
        <AboutSection />
      </section>

      <section id="rooms" class="mb-5">
        <RoomsSection
          :rooms-data="featuredRooms"
          section-title="Our Featured Accommodations"
          section-subtitle="A selection of our finest rooms and suites."
          :initial-display-limit="3"
          :show-view-all-button="true"
        />
      </section>

      <ServiceSection class="mb-5" />
      <PromotionsSection class="mb-5" />
      <GallerySection class="mb-5" />

      <section id="blog" class="mb-5">
        <BlogSection
          :posts-data="latestBlogPosts"
          section-title="Latest From Our Blog"
          section-subtitle="Stay informed with our insights, news, and stories."
          :show-view-all-button="true"
        />
      </section>

      <section id="contact" class="contact-section mb-5">
        <Contact />
      </section>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, watch, onMounted } from "vue";
import { ElMessage } from "element-plus";

// Import components
import AboutSection from "../components/AboutSection.vue";
import RoomsSection from "../components/RoomsSection.vue";
import PromotionsSection from "../components/PromotionsSection.vue";
import GallerySection from "../components/GallerySection.vue";
import Contact from "./Contact.vue";
import BlogSection from "../components/BlogSection.vue";
import ServiceSection from "../components/ServiceSection.vue";


const formRef = ref(null);
const formInline = reactive({
  datein: "",
  dateout: "",
  adults: null,
  child: 0,
});
const isChecking = ref(false);
const availabilityResult = ref(null);
const searchParamsForRooms = ref(null);

// Rules for form validation
const rules = reactive({
  datein: [
    {
      required: true,
      message: "Please select a check-in date",
      trigger: "change",
    },
  ],
  dateout: [
    {
      required: true,
      message: "Please select a check-out date",
      trigger: "change",
    },
    {
      validator: (rule, value, callback) => {
        if (!value) {
          callback(new Error("Please select a check-out date"));
        } else if (new Date(value) <= new Date(formInline.datein)) {
          callback(new Error("Check-out date must be after the check-in date"));
        } else {
          callback();
        }
      },
      trigger: "change",
    },
  ],
  adults: [
    {
      required: true,
      message: "Please select the number of adults",
      trigger: "change",
    },
    {
      type: "number",
      min: 1,
      message: "There must be at least 1 adult",
      trigger: "change",
    },
  ],
});

// Logic to disable dates in the date picker
const disabledDateIn = (time) => time.getTime() < Date.now() - 8.64e7;
const disabledDateOut = (time) => {
  if (formInline.datein) {
    return time.getTime() <= new Date(formInline.datein).getTime();
  }
  return true;
};

// Watcher to auto-update dateout if datein changes
watch(
  () => formInline.datein,
  (newDateIn) => {
    if (!newDateIn) {
      formInline.dateout = "";
      return;
    }
    if (
      formInline.dateout &&
      new Date(formInline.dateout) <= new Date(newDateIn)
    ) {
      const nextDay = new Date(newDateIn);
      nextDay.setDate(nextDay.getDate() + 1);
      formInline.dateout = nextDay;
    } else if (!formInline.dateout) {
      const nextDay = new Date(newDateIn);
      nextDay.setDate(nextDay.getDate() + 1);
      formInline.dateout = nextDay;
    }
  }
);

const performCheckAvailability = async () => {
  const isValid = await formRef.value.validate();
  if (!isValid) {
    ElMessage.error("Please fill in all required fields correctly.");
    return;
  }

  isChecking.value = true;
  availabilityResult.value = null;
  console.log("Checking availability with:", formInline);

  await new Promise((resolve) => setTimeout(resolve, 1500));
  const isAvailable = Math.random() > 0.3;

  if (isAvailable) {
    availabilityResult.value = {
      type: "success",
      title: "Rooms Available!",
      message: `We found suitable options from ${new Date(
        formInline.datein
      ).toLocaleDateString()} to ${new Date(
        formInline.dateout
      ).toLocaleDateString()} for ${formInline.adults} adults and ${
        formInline.child
      } children.`,
    };
    searchParamsForRooms.value = { ...formInline };
    const roomsSectionElement = document.getElementById("rooms");
    if (roomsSectionElement) {
      roomsSectionElement.scrollIntoView({
        behavior: "smooth",
        block: "start",
      });
    }
  } else {
    availabilityResult.value = {
      type: "error",
      title: "No Rooms Found",
      message:
        "Unfortunately, no rooms match your selection. Please try different dates or contact us directly.",
    };
    searchParamsForRooms.value = null;
  }

  isChecking.value = false;
};


</script>

<style scoped>
.custom-button {
  font-weight: bold;
  padding-top: 8px;
  padding-bottom: 8px;
}
.el-card.card-check {
  background-color: #f8f9fa;
  border: none;
}
.el-form-item {
  margin-bottom: 0;
}
.mb-5 {
  margin-bottom: 3rem !important;
}
</style>
