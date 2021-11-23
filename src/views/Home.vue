<template>
  <div class="flex flex-col h-screen max-h-screen">
    <!-- Search -->
    <div class="z-20 flex justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32">
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
          <div class="flex">
            <input v-model="queryIP" class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none" type="text" placeholder="Search for any IP address or leave empty to get your IP info">
            <i @click="getIpInfo" class="cursor-pointer bg-black text-white px-4 rounded-tr-mdrounded-br-md flex items-center fas fa-chevron-right "></i>
          </div>
      </div>
      <!-- IPInfo -->
      <IPInfo v-if="ipInfo" :ipInfo="ipInfo"/>
    </div>
    <!-- Map -->
     <div id="map" class="h-full z-10"></div>
  </div>
</template>

<script>
// @ is an alias to /src
import IPInfo from '../components/IPInfo.vue';
import leaflet from 'leaflet';
import axios from 'axios';
import { onMounted, ref } from 'vue';

export default {
  name: 'Home',
  components: {
    IPInfo
  },
  setup() {
    let mymap;

    const queryIP = ref("");
    const ipInfo = ref(null);

    onMounted(() => {
      mymap = leaflet.map('map').setView([48.856, 2.352], 11);
      leaflet.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiZHJvaWRiYXJiZXIiLCJhIjoiY2t3YjB2N2J6MGFwZjJ2cWs4eDc5MnoyOCJ9.ae-yFxqiiCbs0ewicL6y-g', {
      attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/  copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.  mapbox.com/">Mapbox</a>',
      maxZoom: 18,
      id: 'mapbox/streets-v11',
      tileSize: 512,
      zoomOffset: -1,
      accessToken: 'pk.eyJ1IjoiZHJvaWRiYXJiZXIiLCJhIjoiY2t3YjB2N2J6MGFwZjJ2cWs4eDc5MnoyOCJ9.ae-yFxqiiCbs0ewicL6y-g'
      }).addTo(mymap);
    });
    const getIpInfo = async () => {
      try {
        const data = await axios.get(`https://geo.ipify.org/api/v2/country?apiKey=at_5Xcz38DSvUIS6s5vGbYY1EJcRzjUc&ipAddress=${queryIP.value}`);
        const result = data.data;
        ipInfo.value = {
          address: result.ip,
          state: result.location.region,
          timezone: result.location.timezone,
          isp: result.isp,
          lat: result.location.lat,
          lng: result.location.lng,
        }
        leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(mymap);
        mymap.setView([ipInfo.value.lat, ipInfo.value.lng], 13);
      } catch (error) {
        alert(error.message);
      }
    }
    return { queryIP, ipInfo, getIpInfo };
  },
}
</script>
