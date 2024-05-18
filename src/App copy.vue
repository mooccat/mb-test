<script setup lang="ts">
import vDeckGL from "./components/map/deckgl/vDeckGL.vue";
import vMapbox from "./components/map/mapbox/vMapbox.vue";
import { Deck } from "@deck.gl/core";
import { GeoJsonLayer } from "@deck.gl/layers";
import { onMounted } from "vue";

const INITIAL_VIEW_STATE = {
  latitude: 29,
  longitude: 106,
  zoom: 12,
};
onMounted(() => {
  const deck = new Deck({
    initialViewState: INITIAL_VIEW_STATE,
    controller: true,
    layers: [
      new GeoJsonLayer({
        data: "/500000.json",
        stroked: true,
        filled: true,
        extruded: true,
        wireframe: true,
        getElevation: (f) => Math.sqrt(f.properties.valuePerSqm) * 10,
        getFillColor: [255, 0, 0, 128],
        getLineColor: [0, 0, 0],
      }),
    ],
  });
});
</script>

<template>
  <div class="test">
    <canvas id="deck-canvas"></canvas>
  </div>
</template>

<style scoped>
.fill-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.test {
  width: 100vw;
  height: 100vh;
}
.layer {
  width: 100%;
  height: 100%;
}
</style>
