<template>
<div class="row">
  <l-map
    ref="map"
    style="height: 84.3vh"
    :zoom="zoom"
    :center="center"
  >
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <l-marker
      v-for="item of markerLatLng"
      :key="item.id"
      :lat-lng="[item.latitude, item.longitude]"
      @click="centerMapMarker(item.latitude, item.longitude, item.id)"
    >
    </l-marker>
  </l-map>
  </div>
</template>

<script>
import {LMap, LTileLayer, LMarker} from 'vue2-leaflet';

export default {
  name: 'map',
  components: {
    LMap,
    LTileLayer,
    LMarker
  },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 5,
      center: [51.505, -0.159],
    };
  },
  computed: {
    markerLatLng: {
      get () {
        return this.$store.state.mapData.markerLatLng
      },
      set (val) {
        this.$store.commit('mapData/updateMarkerLatLng', val)
      }
    },
  },
  created () {
    this.$root.$on('centerMapMarker', this.centerMapMarker)
  },

  beforeDestroy () {
    this.$root.$off('centerMapMarker', this.centerMapMarker)
  },
  methods: {
    centerMapMarker(latitude, longitude, id) {
      this.$refs.map.setZoom(5)
      this.$refs.map.setCenter([latitude, longitude])
      this.$store.dispatch('mapData/updateId', id)
    },
  },
}
</script>
