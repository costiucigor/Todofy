<script setup lang="ts">
import { ref, Ref, defineEmits } from 'vue';
import IconEdit from '~icons/mdi/application-cog-outline'

interface Emits {
  (event: 'selectedPicture', picture: string): void;
}

const props = defineProps({
  showModal: {
    type: Boolean,
    required: true,
  },
});

const emits: Emits = defineEmits(['selectedPicture']);

const showModal: Ref<boolean> = ref(false);
const selectedPicture: Ref<string> = ref('');
const pictures: string[] = [
  'src/assets/istockphoto-635792246-612x612.jpg',
  'src/assets/panorama-nature-hills-moldova-near-balanesti-village-193107868.jpg',
  'https://picsum.photos/seed/picture3/500/500',
  'https://picsum.photos/seed/picture4/500/500',
  'https://picsum.photos/seed/picture5/500/500',
];

const selectPicture = (picture: string) => {
  selectedPicture.value = picture;
  showModal.value = false;
  emits('selectedPicture', picture);
};
</script>

<template>
  <div>
    <button @click="showModal = true">
      <IconEdit />
    </button>
    <div
        v-if="showModal"
        class="fixed top-0 left-0 w-full h-full bg-black opacity-50 z-10"
        @click="$emit('update:showModal', false)"
    ></div>
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

