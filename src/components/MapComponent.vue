<template>
  <div class="map-container">
    <l-map
      class="map"
      :zoom='zoom'
      :center='center'
      :options='mapOptions'
      @update:center='centerUpdate'
      @update:zoom='zoomUpdate'
    >
      <l-tile-layer
        :url='url'
        :attribution='attribution'
      />
      <l-marker v-for="(fire, index) in firesData"
        v-bind:key="index"
        :lat-lng='[fire.latitude, fire.longitude]'>
        <l-tooltip>{{ fire.date }}</l-tooltip>
      </l-marker>
    </l-map>
  </div>
</template>

<script>
import { Icon } from 'leaflet'
import { LMap, LTileLayer, LMarker, LTooltip } from 'vue2-leaflet'
import '@/assets/styles/MapComponent.scss'
// https://vue2-leaflet.netlify.app/quickstart/#marker-icons-are-missing
delete Icon.Default.prototype._getIconUrl
Icon.Default.mergeOptions({
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png')
})

export default {
  name: 'MapComponent',
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LTooltip
  },
  props: ['firesData'],
  data () {
    return {
      zoom: 1.2,
      center: [45.4408, 12.3155],
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors', // https://www.openstreetmap.org/copyright
      currentZoom: 1.2,
      currentCenter: [45.4408, 12.3155],
      mapOptions: {
        zoomSnap: 0.5
      }
    }
  },
  methods: {
    zoomUpdate (zoom) {
      this.currentZoom = zoom
    },
    centerUpdate (center) {
      this.currentCenter = center
    }
  }
}
</script>
