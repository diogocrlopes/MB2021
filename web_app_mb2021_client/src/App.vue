<template>
  <l-map
      ref="myMap"
      :center="center"
      :options="mapOptions"
      :zoom="zoom"
      style="height: 900px"
      @dblclick="onMapClick"
  >
    <l-tile-layer :attribution="attribution" :url="url"></l-tile-layer>
    <l-tile-layer
        :attribution="attributionsea"
        :url="urlsea"
        :zIndex=5
    />
  </l-map>
</template>

<script>
import {LMap, LTileLayer} from 'vue2-leaflet';
import 'leaflet/dist/leaflet.css';

export default {
  name: "App",
  components: {
    LMap,
    LTileLayer,
  },
  data:() => {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
          '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      urlsea: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
      attributionsea: 'Map data: &copy; <a href="http://www.openseamap.org">OpenSeaMap</a> contributors',
      zoom: 15,
      mapOptions: {
        zoomSnap: 0.1,
        zoomControl: false,
        keyboard: false,
        doubleClickZoom: false,
      },
      center: [-22.91366005603587, -43.15747802517363],
    }
  },
  methods: {
    onMapClick(event) {
      console.log(event.latlng)
    },
  },
};

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
