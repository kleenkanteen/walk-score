<template>
  <div class="map-wrap">
    <a href="https://www.maptiler.com" class="watermark"
      ><img
        src="https://api.maptiler.com/resources/logo.svg"
        alt="MapTiler logo"
    /></a>
    <div class="map" ref="mapContainer"></div>
  </div>
</template>

<script>
import { shallowRef, onMounted, onUnmounted, markRaw } from "vue"
import { Map, NavigationControl, Marker } from "maplibre-gl"
import exampleFeatureCollection from "../mocks/exampleFeatureCollection.json"
import supermarketData from "../mocks/Seattle/supermarket.json"
import foodData from "../mocks/Seattle/food.json"
import fitnessCentreData from "../mocks/Seattle/fitnessCentre.json"
import schoolData from "../mocks/Seattle/school.json"

export default {
  name: "Map",
  setup() {
    const mapContainer = shallowRef(null)
    const map = shallowRef(null)
    const config = useRuntimeConfig()

    onMounted(() => {
      const apiKey = config.public.maptilerApiKey

      const initialState = { lng: -122.3328, lat: 47.6061, zoom: 10 }

      map.value = markRaw(
        new Map({
          container: mapContainer.value,
          style: `https://api.maptiler.com/maps/streets-v2/style.json?key=${apiKey}`,
          center: [initialState.lng, initialState.lat],
          zoom: initialState.zoom
        })
      )

      map.value.addControl(new NavigationControl(), "top-right")
      // new Marker({ color: "#FF0000" })
      //   .setLngLat([139.7525, 35.6846])
      //   .addTo(map.value)

      const convertNodesToGeoJSON = (nodes) => {
        return {
          type: "FeatureCollection",
          features: nodes.map((node) => ({
            type: "Feature",
            geometry: {
              type: "Point",
              coordinates: [node.lon, node.lat]
            },
            properties: {
              name: node.tags?.name
            }
          }))
        }
      }

      map.value.on("load", async () => {
        const image = await map.value.loadImage(
          "https://maplibre.org/maplibre-gl-js/docs/assets/osgeo-logo.png"
        )
        map.value.addImage("custom-marker", image.data)
        map.value.addSource("supermarket", {
          type: "geojson",
          data: convertNodesToGeoJSON(supermarketData.elements)
        })

        const mapDataList = [
          { ...supermarketData, id: "supermarket" },
          { ...foodData, id: "food" },
          { ...fitnessCentreData, id: "fitnessCentre" },
          { ...schoolData, id: "school" }
        ]

        // mapDataList.forEach((data) => {
        //   map.value.addSource(data.id, {
        //     type: "geojson",
        //     data: convertNodesToGeoJSON(data.elements)
        //   })
        // })

        // https://maplibre.org/maplibre-style-spec/layers/
        map.value.addLayer({
          id: "supermarket",
          type: "circle",
          source: "supermarket",
          paint: {
            "circle-radius": 8,
            "circle-color": "#007cbf",
            "circle-opacity": 0.8
          }
        })
      })
    }),
      onUnmounted(() => {
        map.value?.remove()
      })

    return {
      map,
      mapContainer
    }
  }
}
</script>

<style scoped>
@import "maplibre-gl/dist/maplibre-gl.css";

.map-wrap {
  position: relative;
  width: 100%;
  height: calc(
    100vh - 77px
  ); /* calculate height of the screen minus the heading */
}

.map {
  position: absolute;
  width: 100%;
  height: 100%;
}

.watermark {
  position: absolute;
  left: 10px;
  bottom: 10px;
  z-index: 999;
}
</style>
