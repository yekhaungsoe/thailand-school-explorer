
<script setup>
import { onMounted, onBeforeUnmount, ref } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";

const props = defineProps({
  latitude: {
    type: Number,
    required: true
  },
  longitude: {
    type: Number,
    required: true
  }
});

const mapElement = ref(null);
let map = null;
let marker = null;

onMounted(() => {
  map = L.map(mapElement.value).setView(
    [props.latitude, props.longitude],
    13
  );

  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution: "&copy; OpenStreetMap contributors",
    maxZoom: 19
  }).addTo(map);

  marker = L.marker([props.latitude, props.longitude]).addTo(map);
});

onBeforeUnmount(() => {
  if (map) {
    map.remove();
    map = null;
  }
});
</script>

<template>
  <div ref="mapElement" class="school-map"></div>
</template>

<style scoped>
.school-map {
  width: 100%;
  height: 350px;
  margin-top: 20px;
  border-radius: 8px;
}
</style>
