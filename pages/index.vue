<template>
  <div class="app">
    <Navbar />
    <div class="flex gap-2">
      <SelectMenu />
      <Map />
    </div>
  </div>
</template>

<script>
import Navbar from "../components/Navbar.vue"
import Map from "../components/Map.vue"
import SelectMenu from "../components/SelectMenu.vue"

export default {
  name: "App",
  components: {
    Navbar,
    Map,
    SelectMenu
  }
}
</script>

<style>
@import "maplibre-gl/dist/maplibre-gl.css";

body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
}

.app {
  text-align: center;
}
</style>
