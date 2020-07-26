<template>
  <div class="home">
    <div class="form">
      <form @submit.prevent="sendData">
        <input type="name" v-model="username" placeholder="Nombres" required> <br>
        <input type="text" v-model="location[0]" placeholder="latitud" required> <br>
        <input type="text" v-model="location[1]" placeholder="longitud" required> <br>
        <input type="number" v-model.number="state" placeholder="estado" required> <br>
        <select v-model="type" placeholder="Tipo" required>>
          <option value="taxi">Taxi</option>
          <option value="peaton">Peaton</option>
        </select> <br>
        <span>{{ now }}</span> <button type="button" @click="refreshDate" >Actualizar fecha</button> <br><br>
        <button type="submit">Enviar Datos</button>
      </form>
    </div>
    <br><br>
    <div class="map">
      <button @click="listData">Refrescar Mapa</button><br>
      <div id="mapid">
        <div style="height: 350px;">
          <div class="info" style="height: 15%">
            <!-- <span>Center: {{ center }}</span><br>
            <span>Zoom: {{ zoom }}</span><br>
            <span>Bounds: {{ bounds }}</span><br> -->
          </div>
          <l-map
            style="height: 80%; width: 100%"
            :zoom="zoom"
            :center="center"
            @update:zoom="zoomUpdated"
            @update:center="centerUpdated"
            @update:bounds="boundsUpdated"
          >
            <l-tile-layer :url="url"></l-tile-layer>
            <l-marker v-for="pin in pins" :key="`pin${pin.id}`" :lat-lng="pin.location" ></l-marker>
            <l-circle-marker v-for="pin in pins" :key="`circle${pin.id}`" 
              :lat-lng="pin.location"
              :radius="radius"
              color="red"
            />
          </l-map>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import axios from 'axios'
// import L from 'leaflet'
import { LMap, LTileLayer, LMarker, LCircleMarker } from 'vue2-leaflet'

export default {
  name: 'Home',
  data: () => ({
    moment: moment,
    url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
    zoom: 7,
    center: [-12.048404, -77.042615],
    bounds: null,
    pins: [],
    // Formulario
    username: null,
    location: [null,null],
    state: null,
    type: null,
    now: new Date(),
    // Radio del marcador
    radius: 1.5,
  }),
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LCircleMarker,
  },
  mounted() {
    this.listData()
  },
  methods: {
    zoomUpdated (zoom) {
      this.zoom = zoom;
    },
    centerUpdated (center) {
      this.center = center;
    },
    boundsUpdated (bounds) {
      this.bounds = bounds;
    },
    refreshDate() {
      this.now = new Date()
    },
    sendData() {
      axios.post('http://192.168.20.101:12345/location', {
        username: this.username,
        location: this.location,
        state: this.state,
        type: this.type,
        time: this.now,
      }).then(() => console.log('enviado'))
      .catch((e) => console.error(e))
    },
    listData() {
      this.pins = []
      axios.get('http://192.168.20.101:12345/location')
      .then((res) => {
        res.data.forEach((pin) => {
          pin.location = pin.location.map(l => parseFloat(l))
          this.pins.push(pin)
        })
      })
      .catch((e) => console.error('[listData]',e))
    },
  }
}
</script>

<style lang="sass" scoped>
#map-template
  width: 100px
  height: 100px
</style>