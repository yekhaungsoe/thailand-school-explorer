<script setup>
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
import { API_URL } from "../config";
import SchoolCard from "../components/SchoolCard.vue";

const router = useRouter();

// School list state
const schools = ref([]);
const total = ref(0);
const loading = ref(false);
const errorMessage = ref("");
const searchText = ref("");

// Location state
const provinces = ref([]);
const districts = ref([]);
const subdistricts = ref([]);

const selectedProvinceId = ref("");
const selectedDistrictId = ref("");
const selectedSubdistrictId = ref("");

const locationError = ref("");
const loadingProvinces = ref(false);
const loadingDistricts = ref(false);
const loadingSubdistricts = ref(false);

function openSchool(id) {
  router.push({
    name: "school-detail",
    params: { id }
  });
}

async function loadSchools() {
  if (
    loading.value ||
    loadingDistricts.value ||
    loadingSubdistricts.value
  ) {
    return;
  }

  loading.value = true;
  errorMessage.value = "";
  schools.value = [];
  total.value = 0;

  try {
    const params = new URLSearchParams();
    const keyword = searchText.value.trim();

    if (keyword !== "") {
      params.set("keyword", keyword);
    }

    if (selectedProvinceId.value !== "") {
      params.set("province_id", selectedProvinceId.value);
    }

    if (selectedDistrictId.value !== "") {
      params.set("district_id", selectedDistrictId.value);
    }

    if (selectedSubdistrictId.value !== "") {
      params.set("subdistrict_id", selectedSubdistrictId.value);
    }

    params.set("limit", "20");
    params.set("offset", "0");

    const response = await fetch(`${API_URL}/schools?${params}`);

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    const data = await response.json();

    schools.value = data.items;
    total.value = data.total;
  } catch (error) {
    errorMessage.value = error.message;
  } finally {
    loading.value = false;
  }
}

async function loadProvinces() {
  loadingProvinces.value = true;
  locationError.value = "";

  try {
    const response = await fetch(`${API_URL}/provinces`);

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    provinces.value = await response.json();
  } catch (error) {
    locationError.value =
      `Could not load provinces: ${error.message}`;
  } finally {
    loadingProvinces.value = false;
  }
}

async function onProvinceChanged() {
  selectedDistrictId.value = "";
  selectedSubdistrictId.value = "";

  districts.value = [];
  subdistricts.value = [];
  locationError.value = "";

  if (selectedProvinceId.value === "") {
    return;
  }

  loadingDistricts.value = true;

  try {
    const params = new URLSearchParams({
      province_id: selectedProvinceId.value
    });

    const response = await fetch(`${API_URL}/districts?${params}`);

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    districts.value = await response.json();
  } catch (error) {
    locationError.value =
      `Could not load districts: ${error.message}`;
  } finally {
    loadingDistricts.value = false;
  }
}

async function onDistrictChanged() {
  selectedSubdistrictId.value = "";
  subdistricts.value = [];
  locationError.value = "";

  if (selectedDistrictId.value === "") {
    return;
  }

  loadingSubdistricts.value = true;

  try {
    const params = new URLSearchParams({
      district_id: selectedDistrictId.value
    });

    const response = await fetch(
      `${API_URL}/subdistricts?${params}`
    );

    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }

    subdistricts.value = await response.json();
  } catch (error) {
    locationError.value =
      `Could not load subdistricts: ${error.message}`;
  } finally {
    loadingSubdistricts.value = false;
  }
}

function clearSearch() {
  searchText.value = "";

  selectedProvinceId.value = "";
  selectedDistrictId.value = "";
  selectedSubdistrictId.value = "";

  districts.value = [];
  subdistricts.value = [];
  locationError.value = "";

  loadSchools();
}

onMounted(() => {
  loadSchools();
  loadProvinces();
});
</script>

<template>
  <main class="page">
    <h1>Schools</h1>

    <form class="controls" @submit.prevent="loadSchools">
      <div class="field">
        <label for="province">Province</label>

        <select
          id="province"
          v-model="selectedProvinceId"
          :disabled="
            loading ||
            loadingProvinces ||
            loadingDistricts ||
            loadingSubdistricts
          "
          @change="onProvinceChanged"
        >
          <option value="">All provinces</option>

          <option
            v-for="province in provinces"
            :key="province.id"
            :value="String(province.id)"
          >
            {{ province.name_th }}
          </option>
        </select>
      </div>

      <div class="field">
        <label for="district">District</label>

        <select
          id="district"
          v-model="selectedDistrictId"
          :disabled="
            loading ||
            !selectedProvinceId ||
            loadingDistricts ||
            loadingSubdistricts
          "
          @change="onDistrictChanged"
        >
          <option value="">All districts</option>

          <option
            v-for="district in districts"
            :key="district.id"
            :value="String(district.id)"
          >
            {{ district.name_th }}
          </option>
        </select>
      </div>

      <div class="field">
        <label for="subdistrict">Subdistrict</label>

        <select
          id="subdistrict"
          v-model="selectedSubdistrictId"
          :disabled="
            loading ||
            !selectedDistrictId ||
            loadingSubdistricts
          "
        >
          <option value="">All subdistricts</option>

          <option
            v-for="subdistrict in subdistricts"
            :key="subdistrict.id"
            :value="String(subdistrict.id)"
          >
            {{ subdistrict.name_th }}
          </option>
        </select>
      </div>

      <div class="field">
        <label for="keyword">School name</label>

        <input
          id="keyword"
          v-model="searchText"
          type="text"
          placeholder="Search school name"
          :disabled="loading"
        />
      </div>

      <div class="actions">
        <button
          type="submit"
          :disabled="
            loading ||
            loadingProvinces ||
            loadingDistricts ||
            loadingSubdistricts
          "
        >
          Search
        </button>

        <button
          type="button"
          :disabled="
            loading ||
            loadingProvinces ||
            loadingDistricts ||
            loadingSubdistricts
          "
          @click="clearSearch"
        >
          Clear
        </button>
      </div>
    </form>

    <p v-if="loadingProvinces">
      Loading provinces...
    </p>

    <p v-if="loadingDistricts">
      Loading districts...
    </p>

    <p v-if="loadingSubdistricts">
      Loading subdistricts...
    </p>

    <p v-if="locationError" class="error">
      {{ locationError }}
    </p>

    <p v-if="loading">
      Loading schools...
    </p>

    <p v-else-if="errorMessage" class="error">
      {{ errorMessage }}
    </p>

    <p v-else-if="schools.length === 0">
      No schools found.
    </p>

    <section v-else>
      <p>
        Showing {{ schools.length }} of {{ total }} matching schools
      </p>

      <SchoolCard
        v-for="school in schools"
        :key="school.id"
        :school="school"
        @select="openSchool"
      />
    </section>
  </main>
</template>

<style scoped>
.controls {
  display: flex;
  flex-wrap: wrap;
  align-items: flex-end;
  gap: 12px;
  margin-bottom: 20px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 6px;
  flex: 1 1 200px;
  min-width: 0;
}

input,
select {
  width: 100%;
  box-sizing: border-box;
  padding: 8px 12px;
  font: inherit;
}

.actions {
  display: flex;
  gap: 8px;
}

button {
  padding: 8px 12px;
  font: inherit;
  cursor: pointer;
}

button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

.error {
  color: #b91c1c;
}
</style>