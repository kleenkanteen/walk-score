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

      new Marker({ color: "#FF0000" })
        .setLngLat([initialState.lng, initialState.lat])
        .addTo(map.value)

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

      const mapDataList = [
        {
          ...supermarketData,
          id: "supermarket",
          color: "red",
          opacity: 0.8,
          radius: 8
        },
        { ...foodData, id: "food", color: "blue", opacity: 0.7, radius: 7 },
        {
          ...fitnessCentreData,
          id: "fitnessCentre",
          color: "green",
          opacity: 0.6,
          radius: 6
        },
        {
          ...schoolData,
          id: "school",
          color: "yellow",
          opacity: 0.5,
          radius: 5
        }
      ]

      map.value.on("load", async () => {
        mapDataList.forEach((data) => {
          map.value.addSource(data.id, {
            type: "geojson",
            data: convertNodesToGeoJSON(data.elements)
          })
        })

        // https://maplibre.org/maplibre-style-spec/layers/
        mapDataList.forEach((data) => {
          map.value.addLayer({
            id: data.id,
            type: "circle",
            source: data.id,
            paint: {
              "circle-radius": data.radius,
              "circle-color": data.color,
              "circle-opacity": data.opacity
            }
          })
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
