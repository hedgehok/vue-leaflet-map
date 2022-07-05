<template>
  <div class="home">
    <l-map v-model="zoom" v-model:zoom="zoom" :center="[55.750518, 37.619937]">
      <l-tile-layer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"></l-tile-layer>

      <template v-for="track in tracks" :key="track.index">
        <l-polyline
          :lat-lngs="track.coords"
          :color="track.color"
          :weight="5"
        >
          <l-tooltip v-if="track.name" :sticky="true">{{ track.name }}</l-tooltip>
        </l-polyline>
      </template>
    </l-map>
  </div>
</template>

<script>
import {
  LMap,
  // LIcon,
  LTileLayer,
  // LMarker,
  // LControlLayers,
  LTooltip,
  // LPopup,
  LPolyline,
  // LPolygon,
  // LRectangle,
} from "@vue-leaflet/vue-leaflet";
import "leaflet/dist/leaflet.css"
import { ref } from 'vue';
import common from '../common';
import alex from '../alex';
import roman from '../roman';

export default {
  name: 'HomeView',
  components: {
    LMap,
    LTileLayer,
    LTooltip,
    LPolyline
  },
  setup() {
    const zoom = ref(6);

    const getTracks = (kmls, color) => {
      return kmls.map((kml, index) => {
        const parsed = new DOMParser().parseFromString(kml, "text/xml");
        const doc = parsed.querySelector('Document');
        return {
          index,
          color,
          name: (doc.querySelector('name')) ? doc.querySelector('name').textContent : '',
          coords: doc.querySelector('LineString coordinates').textContent
                        .replaceAll('\n', ' ').replace(/\s\s+/g, ' ').split(' ')
                        .filter(item => item !== '' && item !== undefined && item !== null)
                        .map(item => [+item.split(',')[1], +item.split(',')[0]])
        }
      })
    };

    const tracks = [];
    tracks.push(...getTracks(common, '#3388ff'));
    tracks.push(...getTracks(alex, 'orange'));
    tracks.push(...getTracks(roman, 'red'));

    return {
      zoom,
      tracks
    }
  }
}
</script>

<style lang="scss">
.home {
  width: 100%;
  height: 100%;
}
</style>