<template>
  <div style="min-height: fit-content">
    <l-map
        ref="myMap"
        :center="center"
        :options="mapOptions"
        :zoom="zoom"
        style="height: 960px;"
        @dblclick="onMapClick"
    >
      <l-tile-layer :attribution="attribution" :url="url"></l-tile-layer>
      <l-tile-layer
          :attribution="attributionsea"
          :url="urlsea"
          :zIndex=5
      />
      <l-control position="topright" >
        <div class="panel">
          <p>Latitude: --</p>
          <p>Longitude: --</p>
          <p>Proa: -- °</p>
          <p>SOG: -- nós</p>
          <p>Profundidade: -- m</p>
          <p>Temperatura: -- °C</p>
          <p>Hora: {{ hour }}</p>
        </div>
        <div style="margin-top: 5px" class="panel">
          <p>Planejador de Navegação</p>

        </div>
      </l-control>
      <Vessel
          :vessel-coord="latLngYaw"
      />
    </l-map>
  </div>

</template>

<script>
import {LMap, LTileLayer, LControl} from 'vue2-leaflet';
import 'leaflet/dist/leaflet.css';
import Vessel from "@/components/Vessel";

export default {
  name: "App",
  components: {
    Vessel,
    LMap,
    LTileLayer,
    LControl
  },
  data:() => {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
          '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      urlsea: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
      attributionsea: 'Map data: &copy; <a href="http://www.openseamap.org">OpenSeaMap</a> contributors',
      zoom: 30,
      mapOptions: {
        zoomSnap: 0.1,
        zoomControl: false,
        keyboard: false,
        doubleClickZoom: false,
      },
      center: [-22.91366005603587, -43.15747802517363],
      hour: Date.now(),
      latLngYaw: [-22.91366005603587, -43.15747802517363, 140]
    }
  },
  computed: {
    hour() {
      return Date.now()
    }
  },
  methods: {
    onMapClick(event) {
      console.log(event.latlng)
    },
    zeroPadding(num,digit) {
      let zero = '';
      for(var i = 0; i < digit; i++) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    },
    setTime() {
      setInterval(() =>{
        const date = new Date()
        //TODO: Correct the display of the hour
        this.hour = this.zeroPadding(date.getHours(),2) + ":" + this.zeroPadding(date.getMinutes(),2) + ":" + this.zeroPadding(date.getSeconds(),2)}
      ,1000)
    },
  },
  mounted() {
    this.setTime()
  },
};

</script>

<style>
@import '~leaflet/dist/leaflet.css';

.panel{
  background: azure;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  transition: 0.3s;
  border-radius: 5px;
  padding: 5px;
}
</style>
