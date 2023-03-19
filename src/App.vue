<script setup lang="ts">
import ExtensionComponent from './components/ExtensionComponent.vue'
import ImagePicker from "./components/ImagePicker.vue";
import { Ref, ref, watch } from "vue";

const showModal: Ref<boolean> = ref(false);
const selectedPicture: Ref<string> = ref("");

const saveSelectedPicture = (value: string) => {
  if (chrome?.storage) {
    chrome.storage.sync.set({ selectedPicture: value }, () => {
      console.log("Selected picture saved:", value);
    });
  } else {
    localStorage.setItem("selectedPicture", value);
  }
};

watch(selectedPicture, (newValue) => {
  saveSelectedPicture(newValue);
});

const handleSelectedImage = (picture: string) => {
  selectedPicture.value = picture;
  showModal.value = false;
  saveSelectedPicture(picture);
};

if (chrome?.storage) {
  chrome.storage.sync.get(["selectedPicture"], (result) => {
    if (result.selectedPicture) {
      selectedPicture.value = result.selectedPicture;
    }
  });
} else {
  const storedPicture = localStorage.getItem("selectedPicture");
  if (storedPicture) {
    selectedPicture.value = storedPicture;
  }
}
</script>

<template>
  <div class="absolute top-0 left-0 w-full h-full bg-cover bg-center" :style="{ backgroundImage: `url(${selectedPicture ? selectedPicture : 'src/assets/JMiyEP.jpg'})` }">
    <div class="ml-14 mt-14 w-60 h-60 absolute">
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