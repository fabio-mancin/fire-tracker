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
      </l-marker>
    </l-map>
  </div>
</template>

<script>
import { latLng, Icon } from 'leaflet'
import { LMap, LTileLayer, LMarker/* ,  LTooltip */ } from 'vue2-leaflet'
import '@/assets/styles/MapComponent.scss'

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
    LMarker
  },
  props: ['firesData'],
  data () {
    return {
      zoom: 1.2,
      center: latLng(45.4408, 12.3155),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 1.2,
      currentCenter: latLng(45.4408, 12.3155),
      showParagraph: false,
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
