<script lang="ts">
import { ref } from 'vue'

export default {
  props: ["showModal"],
  emits: ["selectedPicture"],

  setup(props, { emit }) {
    const showModal = ref(false)
    const selectedPicture = ref(null)
    const pictures = [
      'https://picsum.photos/seed/picture1/500/500',
      'https://picsum.photos/seed/picture2/500/500',
      'https://picsum.photos/seed/picture3/500/500',
      'https://picsum.photos/seed/picture4/500/500',
      'https://picsum.photos/seed/picture5/500/500',
    ]

    const selectPicture = (picture) => {
      selectedPicture.value = picture
      showModal.value = false
    }

    const handleImageSelect = (image) => {
      selectedPicture.value = image
      emit('selectedImage', selectedPicture.value)
    }

    return {
      showModal,
      selectedPicture,
      pictures,
      selectPicture,
    }
  },
}
</script>

<template>
  <div>
    <button @click="showModal = true">Choose Background Picture</button>

    <!-- Modal backdrop -->
    <div
        v-if="showModal"
        class="fixed top-0 left-0 w-full h-full bg-black opacity-50 z-10"
        @click="showModal = false"
    ></div>

    <!-- Modal content -->
    <div
        v-if="showModal"
        class="fixed top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-white rounded-md shadow-lg z-20"
        style="width: 500px;"
    >
      <div class="p-4">
        <h2 class="text-lg font-bold mb-4">Choose a Background Picture</h2>
        <div class="flex flex-wrap gap-4">
          <div
              v-for="(picture, index) in pictures"
              :key="index"
              class="w-1/4 cursor-pointer"
              @click="selectPicture(picture)"
          >
            <img :src="picture" class="w-full rounded-md shadow-md" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
/* No styles needed */
</style>
