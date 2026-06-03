<script setup>
import { computed } from "vue"
import { useZoom } from "@visuallyjs/browser-ui-vue"
import { BrowserUI } from "@visuallyjs/browser-ui"
import officeLocations from "../office-locations"

const props = defineProps({
  obj: Object,
  model:Object,
  ui:BrowserUI
})

const zoom = useZoom(props.ui)

function getImage() {
  return `/avatars/${props.obj.data.img}`
}

const timezone = computed(() => {
  const locationData = officeLocations.find(loc => loc.name === props.obj.data.location)
  return locationData ? locationData.timezone : ""
})

function getTimezoneOffset(tz) {
  return tz.match(/\((UTC[+-]\d+)\)/)?.[1] || tz;
}

</script>

<template>
  <img :src="getImage()" :alt="obj.data.name"/>
  <div>
    <strong>{{obj.data.name}}</strong>
    <span class="vjs-node-title">{{obj.data.title}}</span>
    <template v-if="zoom > 1">
        <div class="vjs-node-status-container">
            <span :class="['vjs-node-status', obj.data.online ? 'vjs-node-status-online' : 'vjs-node-status-offline']"></span>
            <span class="vjs-node-status-text">{{ obj.data.online ? 'Online' : 'Offline' }}</span>
        </div>
        <a :href="'mailto:' + obj.data.email" class="vjs-node-email">{{obj.data.email}}</a>
        <span class="vjs-node-location">
            {{obj.data.location}}
            <span v-if="timezone" class="vjs-node-timezone">({{getTimezoneOffset(timezone)}})</span>
        </span>
    </template>
  </div>
</template>

