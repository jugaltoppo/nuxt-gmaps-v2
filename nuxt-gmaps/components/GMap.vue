<template>
  <div class="GMap">
    <div ref="map" class="GMap__Wrapper" :style="`height: ${mapHeight}`"></div>
    <slot />
  </div>
</template>
<script>
import GoogleMapsApiLoader from "google-maps-api-loader";
const MarkerClusterer = require("node-js-marker-clusterer");
export default {
  props: {
    mapHeight: {
      type: String,
      default: ()=>{
        return "400px;"
      }
    },
    options: {
      type: Object,
      default: () => {
        return {};
      },
    },
    language: {},
    zoom: {
      type: Number,
      default: 10,
    },
    cluster: {
      type: Object,
      default: () => {
        return {};
      },
    },
    center: {
      type: Object,
      default: () => {
        return {
          lat: 45.815,
          lng: 15.9819,
        };
      },
    },
    excludeMarkerClusterIndex: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      map: null,
      google: null,
      markerCluster: null,
      jstClusterer: {},
      markers: [],
      events: [
        "bounds_changed",
        "center_changed",
        "click",
        "dblclick",
        "drag",
        "dragend",
        "dragstart",
        "heading_changed",
        "idle",
        "maptypeid_changed",
        "mousemove",
        "mouseout",
        "mouseover",
        "projection_changed",
        "resize",
        "rightclick",
        "tilesloaded",
        "tilt_changed",
        "zoom_changed",
      ],
    };
  },
  async mounted() {
    if (this.$GMaps.loaded === false) {
      this.$GMaps.loaded = true;
      try {
        let GMapSettings = {
          apiKey: this.$GMaps.apiKey,
          language: this.language,
        };
        if (this.$GMaps.libraries !== undefined) {
          GMapSettings["libraries"] = this.$GMaps.libraries;
        }
        const google = GoogleMapsApiLoader(GMapSettings);
        this.$GMaps.google = google;
      } catch (e) {}
    }
    this.google = await this.$GMaps.google;
    this.initMap();
    this.$emit("init", this.google);
    this.$emit("loaded", this.google);
  },
  beforeDestroy() {
    this.$GMaps.loaded = false;
  },
  methods: {
    initMap() {
      this.map = new google.maps.Map(this.$refs.map, {
        center: this.center,
        zoom: this.zoom,
        ...this.options,
      });
      this.initChildren();
      this.events.forEach((event) => {
        this.map.addListener(event, (e) => {
          this.$emit(event, { map: this.map, event: e });
        });
      });
    },
    initChildren() {
      if (this.markers.length > 0) this.markers = [];
      this.$children.forEach((child) => {
        child.init();
      });
      this.map["markers"] = this.markers;
      if (Object.keys(this.cluster).length > 0) {
        this.initCluster();
      }
    },
    initCluster() {
      var results = this.markers.reduce(function (results, org) {
        (results[org.groupId] = results[org.groupId] || []).push(org);
        return results;
      }, {});
      // console.log(Object.entries(results), 'grouped');

      let groupedMarkers = Object.entries(results);

      for (let i = 0; i < groupedMarkers.length; i++) {
        this.jstClusterer[groupedMarkers[i][0]]?.clearMarkers();
      }
      
      for (let i = 0; i < groupedMarkers.length; i++) {
        this.jstClusterer[groupedMarkers[i][0]] = new MarkerClusterer(
          this.map,
          groupedMarkers[i][1].filter((m) => m.getVisible()),
          {
            ...this.cluster.options,
            styles: [
              this.cluster.options.styles.find(
                (e) => e.id == groupedMarkers[i][0]
              ),
            ],
          }
        );
      }
      // if (this.markerCluster !== null) this.markerCluster.clearMarkers();
      // this.markerCluster = new MarkerClusterer(
      //   this.map,
      //   this.markers.filter((m) => m.getVisible()).splice(this.excludeMarkerClusterIndex),
      //   { ...this.cluster.options }
      // );
    },
  },
};
</script>
<style>
.GMap__Wrapper {
  width: 100%;
  /* height: 400px; */
}
</style>
