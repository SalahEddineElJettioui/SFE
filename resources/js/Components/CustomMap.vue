<template>
  <div class="h-screen w-screen relative z-10" id="mapContainer">
    <MapFeatures  class="w-auto absolute lg:left-20 md:right-[85rem] xl:right-[140rem] sm:right-0 top-[1.8rem]" style="z-index: 5000;"></MapFeatures>
  </div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L, { PolyUtil, map, marker } from "leaflet";
import "../../../public/leaflet.curve.js";
import { onMounted, ref } from 'vue';
import markerIcon from "../../../public/images/icons/map-marker-red.svg";
import markerIcon2 from "../../../public/images/icons/map-marker-blue.svg";
import MapFeatures from './MapFeatures.vue';

export default {
  name: "LeafletMap",
  setup () {
    let mymap;
    onMounted(() => {
      mymap = L.map('mapContainer').setView([51.505, -0.09], 10);
      L
        .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1Ijoic2FsYWhqZXR0aW91aSIsImEiOiJjbGdsMXgxYzEwNXdpM2VxaWdneDRhZHN3In0.9LkUVnRagdHkMm1vXWnmyg",
          {
            attribution:
              'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              "pk.eyJ1Ijoic2FsYWhqZXR0aW91aSIsImEiOiJjbGdsMXgxYzEwNXdpM2VxaWdneDRhZHN3In0.9LkUVnRagdHkMm1vXWnmyg",
          }          
        )
        .addTo(mymap);

        getGeolocation();
    });

    const coords = ref(null);
    const fetchCoords = ref(null);
    const geoMarker = ref(null);
    
    const getGeolocation = () => {
      fetchCoords.value = true;
      navigator.geolocation.getCurrentPosition(setCoords, getLocError)
    };
    
    const setCoords = (pos) => {
      fetchCoords.value = true;
      console.log(pos); 
      
      const setSessionCoords = {
        lat: pos.coords.latitude,
        lng: pos.coords.longitude, 
      };
      sessionStorage.setItem('coords',  JSON.stringify(setSessionCoords));
      
      coords.value = setSessionCoords;
      
      plotGeolocation(coords.value);
    };
    
    const plotGeolocation = (coords) => {
      const customMarker = L.icon({
        iconUrl: markerIcon,
        iconSize: [35, 35],
      });
      
      geoMarker.value = L.marker([coords.lat, coords.lng], {
        icon: customMarker
      }).addTo(mymap);
      
      mymap.setView([coords.lat, coords.lng], 10)
    }
    
    const getLocError = (err) => {
      console.log(err);
    };
    
    const resultMarker = ref(null);

    const plotResult = (coords) => {
      if(resultMarker.value){
        mymap.removeLayer(resultMarker.value);
      }

      const customMarker = L.icon({
        iconUrl: markerIcon2,
        iconSize: [35, 35],
      });

      resultMarker.value = L.marker([coords.coordinates[1], coords.coordinates[0]], {
        icon: customMarker
      }).addTo(mymap);

      mymap.setView([coords.coordinates[1], coords.coordinates[0]], 10)


    }

    return { coords, geoMarker };
  },
};
</script>
 