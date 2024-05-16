<template>
  <div :id="$attrs.container"></div>
</template>
<script lang="ts" setup>
import { useAttrs } from "vue";
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import { MAPBOX_SETTINGS } from "../utils/defaultSettings.js";
import { onMounted } from "vue";
const attrs = useAttrs();
const props = defineProps({
  accessToken: {
    type: String,
    required: true,
  },
  map_style: {
    type: String,
    required: false,
    default: MAPBOX_SETTINGS.style,
  },
});
let map = {};
onMounted(() => {
  mapboxgl.accessToken = props.accessToken;
  map = new mapboxgl.Map({
    ...MAPBOX_SETTINGS,
    ...attrs,
    style: props.map_style,
  });
  window.addEventListener("resize", onWindowResizeHandler);
  window.addEventListener("load", resize);
});
const jumpTo = (center, zoom, bearing, pitch) => {
  map.jumpTo({ center, zoom, bearing, pitch });
};
const resize = () => {
  map.resize();
};
const onWindowResizeHandler = () => {
  setTimeout(() => map.resize(), 300);
};
</script>
