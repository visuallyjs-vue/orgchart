<script setup>
import { ref, computed } from "vue"
import { InspectorComponent } from "@visuallyjs/browser-ui-vue"
import officeLocations from "../office-locations"

const props = defineProps({
  onSelect: Function
})

const current = ref(null)
const manager = ref(null)
const reports = ref([])

const timezone = computed(() => {
  if (!current.value) return ""
  const locationData = officeLocations.find(loc => loc.name === current.value.data.location)
  return locationData ? locationData.timezone : ""
})

function getTimezoneOffset(tz) {
  return tz.match(/\((UTC[+-]\d+)\)/)?.[1] || tz;
}

function getImage(person) {
  return `/avatars/${person.data.img}`
}

// invoked by the InspectorComponent when nothing is selected. We clear all our state.
const renderEmptyContainer = () => {
  current.value = null
  manager.value = null
  reports.value = []
}

// Invoked by the InspectorComponent when something is selected. We update our state.
const refresh = (obj) => {
  current.value = obj
  manager.value = obj.getTargetEdges().map(e => e.source)[0]
  reports.value = obj.getSourceEdges().map(e => e.target)
}

function selectPerson(person) {
    if (props.onSelect) {
        props.onSelect(person)
    }
}

</script>

<template>
  <InspectorComponent :refresh="refresh" :renderEmptyContainer="renderEmptyContainer">

    <div v-if="current != null" class="vjs-orgchart-inspector">

      <h1>{{ current.data.name }}</h1>
      <h2>{{ current.data.title }}</h2>

      <div class="vjs-orgchart-inspector-details">
        <div class="vjs-node-status-container">
          <span :class="['vjs-node-status', current.data.online ? 'vjs-node-status-online' : 'vjs-node-status-offline']"></span>
          <span class="vjs-node-status-text">{{ current.data.online ? 'Online' : 'Offline' }}</span>
        </div>
        <a :href="'mailto:' + current.data.email" class="vjs-node-email">{{current.data.email}}</a>
        <span class="vjs-node-location">
          {{current.data.location}}
          <span v-if="timezone" class="vjs-node-timezone"> ({{getTimezoneOffset(timezone)}})</span>
        </span>
      </div>

      <template v-if="manager != null">
        <h5>Reports to:</h5>
        <a class="vjs-orgchart-inspector-person" href="#" :data-id="manager.data.id" @click.prevent="selectPerson(manager)">
          <img :src="getImage(manager)" :alt="manager.data.name"/>
          <div>
            {{ manager.data.name }}
            <span>{{ manager.data.title }}</span>
          </div>
        </a>
      </template>

      <template v-if="reports.length > 0">
        <br/>
        <h5>Reports:</h5>
        <a v-for="r in reports" :key="r.data.id" class="vjs-orgchart-inspector-person" href="#" :data-id="r.data.id" @click.prevent="selectPerson(r)">
          <img :src="getImage(r)" :alt="r.data.name"/>
          <div>
            {{ r.data.name }}
            <span>{{ r.data.title }}</span>
          </div>
        </a>
      </template>

    </div>

  </InspectorComponent>
</template>
