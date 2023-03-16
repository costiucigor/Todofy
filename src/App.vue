<script setup lang="ts">
import ExtensionComponent from './components/ExtensionComponent.vue'
import ImagePicker from "./components//ImagePicker.vue";
import {Ref, ref, watch} from "vue";
components: { ImagePicker }

const showModal: Ref<boolean> = ref(false);
const selectedPicture: Ref<string> = ref('');

const saveSelectedPicture = (value: string) => {
  chrome.storage.sync.set({ selectedPicture: value }, () => {
    console.log("Selected picture save:", value);
  })
}

watch(selectedPicture, (newValue) => {
  saveSelectedPicture(newValue);
})

const handleSelectedImage = (picture) => {
  console.log(selectedPicture.value)
  selectedPicture.value = picture
  showModal.value = false
}

chrome.storage.sync.get(['selectedPicture'], (result) => {
  if (result.selectedPicture) {
    selectedPicture.value = result.selectedPicture;
  }
});

</script>

<template>
  <div class="absolute top-0 left-0 w-full h-full bg-cover bg-center" :style="{ backgroundImage: `url(${selectedPicture ? selectedPicture : 'src/assets/JMiyEP.jpg'})` }">
    <div class="ml-7 mt-7 w-60 h-60 absolute">
      <image-picker v-model:showModal="showModal" @selectedPicture="(image) => handleSelectedImage(image)" />
    </div>
    <extension-component/>
  </div>
</template>

<style>
html, body {
  margin: 0;
  padding: 0;
}
</style>