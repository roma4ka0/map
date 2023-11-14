<template>
  <div class="container">
    <div class="map" ref="mapContainer"></div>
     <button @click="getLocation()">Get Location</button>
    {{ lat }} , {{ lng }}

  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import L from "leaflet";

const lat = ref(0);
const lng = ref(0);
const map = ref();
const mapContainer = ref();
const coordFromServer = ref([]);

onMounted(() => {
  map.value = L.map(mapContainer.value).setView([47.8388, 35.1396], 13);
  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    maxZoom: 19,
    attribution:
      '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
  }).addTo(map.value);

        coordFromServer.value.forEach((coordForMarker) => {
          L.circleMarker([coordForMarker.latitude, coordForMarker.longitude], {
                radius: 4,
                fillColor: "green",
                color: "green",
              }).addTo(map.value);
        });

      })

function getLocation() {
  if (navigator.geolocation) {
    navigator.geolocation.watchPosition((position) => {
      lat.value = position.coords.latitude;
      lng.value = position.coords.longitude;
      map.value.setView([lat.value, lng.value], 13);

      L.marker([lat.value, lng.value], { draggable: true })
        .addTo(map.value)
        .on("dragend", (event) => {
          console.log(event)
        });


    });
  }
}
</script>

<style scoped>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.map {
  width: 1000px;
  height: 600px;
}


</style>