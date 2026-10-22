
<script setup>
import SchoolLocationMap from "../components/SchoolLocationMap.vue";
import { onMounted, ref, watch } from "vue";
import { useRoute } from "vue-router";
import { API_URL } from "../config";

const route = useRoute();

const school = ref(null);
const location = ref(null);
const loading = ref(false);
const errorMessage = ref("");
const locationError = ref("");

async function loadSchool() {
  loading.value = true;
  errorMessage.value = "";
  locationError.value = "";
  school.value = null;
  location.value = null;

  try {
    const response = await fetch(
      `${API_URL}/schools/${route.params.id}`
    );

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    school.value = await response.json();

    const locationResponse = await fetch(
      `${API_URL}/schools/${route.params.id}/location`
    );

    if (!locationResponse.ok) {
      const data = await locationResponse.json();
      throw new Error(data.detail || "School location unavailable");
    }

    location.value = await locationResponse.json();
  } catch (error) {
    if (!school.value) {
      errorMessage.value = error.message;
    } else {
      locationError.value = error.message;
    }
  } finally {
    loading.value = false;
  }
}

watch(() => route.params.id, loadSchool);
onMounted(loadSchool);
</script>

<template>
  <main class="page">
    <RouterLink to="/schools">
      Back to schools
    </RouterLink>

    <p v-if="loading">
      Loading school...
    </p>

    <p v-else-if="errorMessage" class="error">
      {{ errorMessage }}
    </p>

    <section v-else-if="school" class="school-detail">
      <h1>{{ school.name_th }}</h1>

      <p>Province: {{ school.province_name_th }}</p>
      <p>District: {{ school.district_name_th }}</p>
      <p>Subdistrict: {{ school.subdistrict_name_th }}</p>
      <p>SMIS code: {{ school.smis_code }}</p>

      <p v-if="school.name_en">
        English name: {{ school.name_en }}
      </p>

      <h2>School Location</h2>

      <SchoolLocationMap
        v-if="location"
        :latitude="location.latitude"
        :longitude="location.longitude"
      />

      <p v-else-if="locationError" class="error">
        {{ locationError }}
      </p>
    </section>
  </main>
</template>

<style scoped>
.school-detail {
  margin-top: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

.error {
  color: #b91c1c;
}
</style>
