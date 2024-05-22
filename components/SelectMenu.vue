<script setup lang="ts">
import draggable from "vuedraggable"
const buildings = [
  {
    id: 1,
    name: "Church"
  },
  {
    id: 2,
    name: "Gym"
  },
  {
    id: 3,
    name: "Groceries"
  },
  {
    id: 4,
    name: "School"
  },
  {
    id: 5,
    name: "Hospital"
  },
  {
    id: 6,
    name: "Restaurant"
  },
  {
    id: 7,
    name: "Cafe"
  },
  {
    id: 8,
    name: "Takeaway"
  },
  {
    id: 9,
    name: "Park"
  }
]

const selectedBuildings = [
  {
    id: 1,
    name: "Church"
  },
  {
    id: 2,
    name: "Gym"
  },
  {
    id: 3,
    name: "Groceries"
  },
  {
    id: 4,
    name: "School"
  }
]

const addBuilding = () => {
  // TODO
  console.log({ selected: selected.value })
}

const selected = ref(null)
</script>

<template>
  <UContainer class="max-w-sm">
    <UContainer class="py-4 flex gap-2">
      <USelectMenu
        v-model="selected"
        searchable
        searchable-placeholder="Search a building type..."
        class="w-full lg:w-48"
        :options="buildings"
        placeholder="Select building type"
        value-attribute="id"
        option-attribute="name"
      />
      <UButton @click="addBuilding" :disabled="!selected">Add Item</UButton>
    </UContainer>
    <UContainer class="py-4">
      <draggable
        v-model="selectedBuildings"
        group="selectedBuildings"
        @start="drag = true"
        @end="drag = false"
        item-key="id"
      >
        <template #item="{ element, index }">
          <UContainer class="flex flex-col">
            <button class="btn rounded-none">
              {{ index + 1 }} - {{ element.name }}
            </button>
          </UContainer>
        </template>
      </draggable>
    </UContainer>
  </UContainer>
</template>
