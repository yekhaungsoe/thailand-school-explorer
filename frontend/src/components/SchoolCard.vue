<script setup>
const props = defineProps({
  school: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(["select"]);

function selectThisSchool() {
  emit("select", props.school.id);
}
</script>

<template>
  <article class="school-card">
    <h2>{{ school.name_th }}</h2>

    <p>Province: {{ school.province_name_th }}</p>
    <p>District: {{ school.district_name_th }}</p>

    <p v-if="school.subdistrict_name_th">
      Subdistrict: {{ school.subdistrict_name_th }}
    </p>

    <button @click="selectThisSchool">
      View details
    </button>
  </article>
</template>

<style scoped>
.school-card {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
}

button {
  padding: 8px 12px;
  cursor: pointer;
}
</style>