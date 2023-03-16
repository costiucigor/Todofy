<script setup>
import ExtensionComponent from './components/ExtensionComponent.vue'
import ImagePicker from "./components//ImagePicker.vue";
import {ref} from "vue";
components: { ImagePicker }

const showModal = ref(false)
const selectedPicture = ref("")

const handleSelectedImage = (picture) => {
  console.log(selectedPicture.value)
  selectedPicture.value = picture
  showModal.value = false
}

</script>

<template>
  <div class="background" :style="{ backgroundImage: `url(${selectedPicture})`}">
    <image-picker v-model:showModal="showModal" @selectedPicture="(image) => handleSelectedImage(image)" />
    <extension-component/>
  </div>
</template>

<style>
html, body {
  margin: 0;
  padding: 0;
}

.background {
  position: absolute;
  height: 100%;
  width: 100%;
  background-size: cover;
  background-position: center;
}
</style>