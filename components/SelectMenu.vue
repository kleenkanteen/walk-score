<script setup>
import draggable from "vuedraggable"
import { Country, State, City } from "country-state-city"

const buildingsList = [
  {
    name: "School",
    tagKey: "amenity",
    tagValue: "school",
    id: "school"
  },
  {
    name: "Grocery Store",
    tagKey: "shop",
    tagValue: "supermarket",
    id: "grocery"
  },
  {
    name: "Church",
    tagKey: "building",
    tagValue: "church",
    id: "church"
  },
  {
    name: "Mosque",
    tagKey: "building",
    tagValue: "mosque",
    id: "mosque"
  },
  {
    name: "Synagogue",
    tagKey: "building",
    tagValue: "synagogue",
    id: "synagogue"
  },
  {
    name: "Gym",
    tagKey: "leisure",
    tagValue: "fitness_centre",
    id: "gym"
  },
  {
    name: "Swimming Pool",
    tagKey: "leisure",
    tagValue: "swimming_pool",
    id: "swimming_pool"
  },
  {
    name: "Sports Centre",
    tagKey: "leisure",
    tagValue: "sports_centre",
    id: "sports_centre"
  },
  { name: "Mall", tagKey: "shop", tagValue: "mall", id: "mall" },
  {
    name: "Convenience Store",
    tagKey: "shop",
    tagValue: "convenience",
    id: "convenience_store"
  },
  {
    name: "Hospital",
    tagKey: "amenity",
    tagValue: "hospital",
    id: "hospital"
  },
  {
    name: "Pharmacy",
    tagKey: "amenity",
    tagValue: "pharmacy",
    id: "pharmacy"
  },
  {
    name: "Library",
    tagKey: "amenity",
    tagValue: "library",
    id: "library"
  },
  {
    name: "Restaurant",
    tagKey: "amenity",
    tagValue: "restaurant",
    id: "restaurant"
  }
]
const selectedCountry = ref(null)
const selectedState = ref(null)
const selectedCity = ref(null)
const countriesList = ref([])
const statesList = ref([])
const citiesList = ref([])

const selectedBuildingOption = ref(null)
const selectedBuildings = ref([])

onMounted(() => {
  countriesList.value = Country.getAllCountries().map((country) => ({
    name: country.name,
    isoCode: country.isoCode
  }))
})

const addSelectedBuilding = () => {
  const building = buildingsList.find(
    (b) => b.id === selectedBuildingOption.value
  )
  if (!selectedBuildings.value.find((b) => b.id === building.id)) {
    selectedBuildings.value.push({
      ...building,
      order: selectedBuildings.value.length + 1
    })
  }
}

const deleteBuilding = (id) => {
  selectedBuildings.value = selectedBuildings.value.filter((b) => b.id !== id)
}

const onSubmit = () => {
  const result = {
    country: toRaw(selectedCountry.value),
    state: toRaw(selectedState.value),
    city: toRaw(selectedCity.value),
    buildingTypes: toRaw(selectedBuildings.value)
  }
  console.log(result)
}

watch(selectedBuildings, (newBuildings) => {
  selectedBuildings.value.forEach((building, index) => {
    building.order = index + 1
  })
})

watch(selectedCountry, (newCountry) => {
  if (newCountry) {
    statesList.value = State.getStatesOfCountry(newCountry.isoCode).map(
      (state) => ({
        name: state.name,
        isoCode: state.isoCode
      })
    )
    selectedState.value = null
    selectedCity.value = null
  } else {
    statesList.value = []
    citiesList.value = []
  }
})

watch(selectedState, (newState) => {
  if (newState) {
    if (
      City.getCitiesOfState(selectedCountry.value.isoCode, newState.isoCode)
        .length > 0
    ) {
      citiesList.value = City.getCitiesOfState(
        selectedCountry.value.isoCode,
        newState.isoCode
      ).map((city) => ({
        name: city.name,
        countryCode: selectedCountry.value.isoCode,
        stateCode: selectedState.value.isoCode
      }))
      selectedCity.value = null
    } else {
      citiesList.value = [
        {
          name: "No cities found",
          countryCode: selectedCountry.value.isoCode,
          stateCode: selectedState.isoCode
        }
      ]
    }
  } else {
    citiesList.value = []
  }
})

const isAllInfoEntered = computed(() => {
  return (
    selectedCity.value &&
    selectedState.value &&
    selectedCountry.value &&
    selectedBuildings.value.length > 0
  )
})
</script>

<template>
  <UContainer class="py-6 flex flex-col gap-6">
    <!-- select location -->
    <UContainer class="grid gap-2 items-start justify-start">
      <h2>Select City</h2>
      <UInputMenu
        v-model="selectedCountry"
        :options="countriesList"
        option-attribute="name"
        placeholder="Select country"
      />
      <UInputMenu
        v-model="selectedState"
        :options="statesList"
        option-attribute="name"
        :disabled="!selectedCountry"
        placeholder="Select state"
      />
      <UInputMenu
        v-model="selectedCity"
        :options="citiesList"
        option-attribute="name"
        :disabled="!selectedState"
        placeholder="Select city"
      />
    </UContainer>
    <!-- select building types -->
    <UContainer>
      <h2 class="capitalize">Choose and rank building types</h2>
      <div class="flex gap-2 pt-2">
        <USelectMenu
          v-model="selectedBuildingOption"
          searchable
          searchable-placeholder="Search a building type..."
          class="w-full lg:w-48"
          :options="buildingsList"
          placeholder="Select Building Type"
          value-attribute="id"
          option-attribute="name"
        />
        <UButton @click="addSelectedBuilding" :disabled="!selectedBuildings"
          >Add Item</UButton
        >
      </div>
      <draggable
        v-model="selectedBuildings"
        group="selectedBuildings"
        item-key="id"
        class="pt-2"
      >
        <template #item="{ element, index }">
          <div class="flex justify-between gap-2 my-2">
            <button class="btn rounded-none grow">
              {{ index + 1 }} - {{ element.name }}
            </button>
            <UButton
              icon="i-heroicons-trash"
              size="sm"
              color="red"
              square
              variant="solid"
              class="my-2"
              @click="deleteBuilding(element.id)"
            />
          </div>
        </template>
      </draggable>
    </UContainer>
    <!-- Submit -->
    <UButton
      class="my-4 self-center capitalize"
      :disabled="!isAllInfoEntered"
      @click="onSubmit"
      >Show locations on map</UButton
    >
  </UContainer>
</template>
