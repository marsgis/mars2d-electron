<template>
  <MarsMap :url="configUrl" map-key="test" @onload="marsOnload" />
</template>

<script setup lang="ts">
import MarsMap from "./components/mars-work/mars-map.vue";
const configUrl = "config/config.json";

const mars2d = window.mars2d;

const L = mars2d.L;

const marsOnload = (map: any) => { 

};

 
</script>

<style></style>
