# π Nuxt GMaps

[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![npm downloads][kofi-src]][kofi-href]

Try it out here:
[Nuxt.js Google Maps Module](https://codesandbox.io/s/6j6zw48l83)

### π Usage
```bash
npm i nuxt-gmaps
```
```javascript
//nuxt.config.js
modules: [
  ['nuxt-gmaps', {
    key: '<API-Key>',
    //you can use libraries: ['places']
  }]
]
```

### π Updates
- **1.2.4** - Options for GMapInfoWindow
- **1.2.3** - Language prop
- **1.2.2** - Google Map Circle (Documentation: https://developers.google.com/maps/documentation/javascript/examples/circle-simple)

### Documentation
[Official Google Documentation](https://developers.google.com/maps/documentation/javascript/tutorial)

### π§ Options
- key
- libraries

### ποΈ Marker Events
- click
- mouseover
- mouseout

### πΊοΈοΈ Map Event
- init
- loaded
- bounds_changed
- center_changed
- click
- dblclick
- drag
- dragend
- dragstart
- heading_changed
- idle
- maptypeid_changed
- mousemove
- mouseout
- mouseover
- projection_changed
- resize
- rightclick
- tilesloaded
- tilt_changed
- zoom_changed

### Example
```html
<GMap
  ref="gMap"
  language="en"
  :cluster="{options: {styles: clusterStyle}}"
  :center="{lat: locations[0].lat, lng: locations[0].lng}"
  :options="{fullscreenControl: false, styles: mapStyle}"
  :zoom="6"
>
  <GMapMarker
    v-for="location in locations"
    :key="location.id"
    :position="{lat: location.lat, lng: location.lng}"
    :options="{icon: location === currentLocation ? pins.selected : pins.notSelected}"
    @click="currentLocation = location"
  >
    <GMapInfoWindow :options="{maxWidth: 200}">
      <code>
        lat: {{ location.lat }},
        lng: {{ location.lng }}
      </code>
    </GMapInfoWindow>
  </GMapMarker>
  <GMapCircle :options="circleOptions"/>
</GMap>

```
```javascript
data() {
  return {
    currentLocation: {},
    circleOptions: {
      ...
    },
    locations: [
      {
        lat: 44.933076,
        lng: 15.629058
      },
      {
        lat: 45.815,
        lng: "15.9819"
      },
      {
        lat: "45.12",
        lng: "16.21"
      }
    ],
    pins: {
      selected: "data:image/png;base64,iVBORw0KGgo...",
      notSelected: "data:image/png;base64,iVBORw0KGgo..."
    },
    mapStyle: [...],
    clusterStyle: [
      {
        url: "https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m1.png",
        width: 56,
        height: 56,
        textColor: "#fff"
      }
    ]
  }
}
```

<!-- Badges -->
[npm-version-src]: https://badgen.net/npm/v/nuxt-gmaps/latest
[npm-version-href]: https://npmjs.com/package/nuxt-gmaps

[kofi-src]: https://badgen.net/badge/icon/kofi?icon=kofi&label=support
[kofi-href]: https://ko-fi.com/darioferderber

[npm-downloads-src]: https://badgen.net/npm/dm/nuxt-gmaps
[npm-downloads-href]: https://npmjs.com/package/nuxt-gmaps