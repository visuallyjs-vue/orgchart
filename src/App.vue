<script setup>
import { ref } from "vue"

import renderOptions from "./render-options.ts"
import createViewOptions from "./view-options.ts"
import InspectorComponent from "./components/InspectorComponent.vue"

const canvas = ref(null)

// set a person to be the current selection. We also zoom to focus on that individual.s
function selectPerson(obj) {
  canvas.value.model.setSelection(obj)
  canvas.value.surface.centerOnAndZoom(obj, 0.15)
}

const viewOptions = createViewOptions(selectPerson)

</script>

<template>
  <SurfaceProvider>
    <SurfaceComponent ref="canvas" class="vjs-orgchart-canvas" :renderOptions="renderOptions" :viewOptions="viewOptions"  url="/dataset.json">
      <ControlsComponent :undoRedo="false" :clear="false"/>
      <MiniviewComponent/>
    </SurfaceComponent>
    <div class="vjs-orgchart-rhs">
      <InspectorComponent :onSelect="selectPerson"/>
    </div>
  </SurfaceProvider>
</template>

