<template>
  <div id="app">
    <imageView />
  </div>
</template>

<script setup>
import MergePDFs from "./components/MergePDFs.vue"
import imageView from "./components/imageView.vue"

</script>

<style>
html,
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0 auto;
}
</style>
