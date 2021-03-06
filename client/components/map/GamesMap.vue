<template>
  <div class="game-map">
    <Loader v-if="loadingMap || loading" class="game-map__loader" />
    <div id="map" class="game-map__map" :class="mapClass"></div>
  </div>
</template>

<script>
import Vue from "vue";
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import Loader from "@/components/Loader";
import GameMarker from "./GameMarker";
import GamePopup from "./GamePopup";
import Geolocation from "@/services/Geolocation";

export default {
  name: "GamesMap",
  components: {
    Loader,
  },
  props: {
    games: {
      type: Array,
      default() {
        return [];
      },
    },
    loading: {
      type: Boolean,
      default: false,
    },
    center: {
      type: Object,
    },
    zoom: {
      type: Number,
    },
    showPopups: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      map: null,
      markers: [],
      loadingMap: true,
      currentLocation: null,
      defaultCenter: {
        latitude: 46.227638,
        longitude: 2.213749,
      },
    };
  },
  watch: {
    center: {
      handler() {
        this.updateMapCenter();
      },
      deep: true,
    },
    zoomLevel() {
      this.updateMapCenter();
    },
    games() {
      this.removeCurrentMarkers();
      this.renderMarkersAndPopups();
    },
  },
  computed: {
    mapClass() {
      return this.loadingMap ? "game-map__map--hidden" : null;
    },
    centerLocation() {
      if (this.center) {
        return [this.center.longitude, this.center.latitude];
      }
      if (this.currentLocation) {
        return [this.currentLocation.longitude, this.currentLocation.latitude];
      }
      return [this.defaultCenter.longitude, this.defaultCenter.latitude];
    },
    zoomLevel() {
      if (this.zoom) {
        return this.zoom;
      }
      return this.center || this.currentLocation ? 14 : 5;
    },
  },
  methods: {
    initMap() {
      mapboxgl.accessToken = process.env.mapboxAccessToken;
      this.map = new mapboxgl.Map({
        container: document.getElementById("map"),
        style: "mapbox://styles/antoinewebians/ckp76q3me0g8m18qvz3i5uvcj",
        center: this.centerLocation,
        zoom: this.zoomLevel,
      });
      this.attachMapEventListeners();
    },
    renderMarkersAndPopups() {
      this.games.forEach((game) => {
        const markerComponent = new Vue({
          router: this.$router,
          ...GameMarker,
          propsData: { game },
        }).$mount();
        const marker = new mapboxgl.Marker({ element: markerComponent.$el }).setLngLat([game.longitude, game.latitude]);
        this.markers.push(marker);
        if (this.showPopups) {
          const popupComponent = new Vue({
            router: this.$router,
            ...GamePopup,
            propsData: { game },
          }).$mount();
          const popup = new mapboxgl.Popup().setDOMContent(popupComponent.$el);
          marker.setPopup(popup).addTo(this.map);
        } else {
          marker.addTo(this.map);
        }
      });
    },
    removeCurrentMarkers() {
      this.markers.forEach((marker) => marker.remove());
    },
    attachMapEventListeners() {
      this.map.on("load", () => {
        this.renderMarkersAndPopups();
        this.loadingMap = false;
      });
    },
    updateMapCenter() {
      this.map.flyTo({
        center: this.centerLocation,
        zoom: this.zoomLevel,
      });
    },
  },
  mounted() {
    this.initMap();
    Geolocation().then(({ location }) => {
      this.currentLocation = location;
      this.updateMapCenter();
    });
  },
  destroyed() {
    if (this.map) {
      this.map.remove();
    }
  },
};
</script>

<style lang="scss" scoped>
.mapboxgl-ctrl-bottom-left,
.mapboxgl-ctrl-bottom-right,
.mapboxgl-ctrl-top-left,
.mapboxgl-ctrl-top-right {
  z-index: 1;
}
.game-map {
  position: relative;
  height: calc(100vh - 50px);
  overflow: hidden;

  &__loader {
    position: absolute;
    top: 50%;
    right: 50%;
    transform: translate(50%, -50%);
  }

  &__map {
    height: 100%;

    &--hidden {
      opacity: 0;
    }
  }
}
</style>
