<script setup>


const props = defineProps({
  obj: Object,
  model:Object
})

function getImage() {
  return `/avatars/${props.obj.data.img}`
}

</script>

<template>
  <img :src="getImage()" :alt="obj.data.name"/>
  <div>
    <strong>{{obj.data.name}}</strong>
    <span>{{obj.data.title}}</span>
  </div>
</template>

