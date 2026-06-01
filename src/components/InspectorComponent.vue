<script setup>
import { ref } from "vue"
import { InspectorComponent } from "@visuallyjs/browser-ui-vue"

const props = defineProps({
  onSelect: Function
})

const current = ref(null)
const manager = ref(null)
const reports = ref([])

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
