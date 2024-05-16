<template>
  <div v-bind="attrs">
    <slot></slot>
    <canvas id="deck-canvas"></canvas>
  </div>
</template>

<script lang="ts" setup>
import { defineEmits, onMounted, ref, watch, useAttrs } from "vue";
import processChildren from "../utils/processChildren.js";
import { DECKGL_SETTINGS } from "../utils/defaultSettings.js";
import { Deck } from "@deck.gl/core";
const emit = defineEmits(["initialRender"]);
const slots = defineSlots();
const attrs = useAttrs();
const props = defineProps();
console.log("attrs", attrs);
const afterRenderCounter = ref(0);
const layers = ref([]);
const views = ref([]);
const hasHandlers = ref(false);
let map = {};
let deck = {};

onMounted(() => {
  deck = new Deck({
    ...DECKGL_SETTINGS,
    onAfterRender: setupHandlers,
    attrs,
  });

  let processedChildren = processChildren(slots.default());
  map = processedChildren.map;

  processedChildren.layers.forEach((layer) => {
    layer.$on("layerUpdated", updateLayers);
    layers.push(layer.getLayer());
  });

  processedChildren.views.forEach((view) => {
    views.push(view.getView());
  });

  //   window.addEventListener("resize", onWindowResizeHandler);
  //   window.addEventListener("load", onWindowResizeHandler);
});
watch(layers, () => {
  deck.setProps({ layers: [...layers] });
});

const hasInitialRenderBugResolved = () => {
  if (afterRenderCounter.value == 1) {
    return true;
  }
  afterRenderCounter.value++;
  deck.setProps({ ...deck.props });
  return false;
};
const setupHandlers = () => {
  if (hasInitialRenderBugResolved()) {
    deck.setProps({
      ...deck.props,
      views: views.value.length ? views.value : null,
      width: "100%",
      height: "100%",
      onViewStateChange: ({ viewState }) => moveMap({ viewState }),
      onAfterRender: () => {},
    });
    emit("initialRender", true);
    hasHandlers.value = true;
  }
};
</script>
