<template>
  <div class="GMap__Marker" v-if="!markerLoaded">
    <slot v-if="marker === null"/>
  </div>
</template>

<script>
export default {
  props: {
    position: Object,
    options: Object,
  },

  data(){
    return{
      marker: null,
      markerLoaded: false,
      events: ['click', 'mouseover', 'mouseout']
    }
  },

  methods: {
    init(){
      let child = undefined;
      this.position.lat = parseFloat(this.position.lat);
      this.position.lng = parseFloat(this.position.lng);
      this.marker = new this.$parent.google.maps.Marker({
        position: this.position,
        map: this.$parent.map,
        ...this.options
      });

      //added group id for seperate clustering
      if(this.options.groupId){
        this.marker.groupId = this.options.groupId;
      }
      if(this.options.floorLevel){
        this.marker.floorLevel = this.options.floorLevel;
      }

      this.$parent.markers.push(this.marker);
      this.markerLoaded = true;

      if(this.$children.length > 0){
        child = this.$children[0];
        child.initInfoWindow();
      }

      this.events.forEach(event =>{
        this.$parent.google.maps.event.addListener(this.marker, event, (e) =>{
          if(child !== undefined && event === 'click') child.infoWindow.open(this.$parent.map, this.marker);
          this.$emit(event, {position: this.position, event: e, map: this.$parent.map, marker: this.marker})
        });
      })
    }
  },

  watch:{
    'options.icon'(value){
      this.marker.setIcon(value)
    }
  }
}
</script>
