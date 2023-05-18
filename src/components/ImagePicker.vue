<script setup lang="ts">
import {onMounted, ref, Ref} from 'vue';
import IconEdit from '~icons/mdi/application-cog-outline'

interface Emits {
  (event: 'selectedPicture', picture: string): void;
}

onMounted(() => {
  const savedTheme = localStorage.getItem("isDarkTheme");
  if (savedTheme) {
    this.isDarkTheme = savedTheme === "true";
    document.documentElement.classList.toggle("dark", this.isDarkTheme)
  }
})

defineProps({
  showModal: {
    type: Boolean,
    required: true,
  },
});

const emits: Emits = defineEmits(['selectedPicture']);

const showModal: Ref<boolean> = ref(false);
const selectedPicture: Ref<string> = ref("");
const pictures: string[] = [
  "./mountain_bg.jpg",
  "./forest_bg.jpg",
  "./sunset_bg.jpg",
  "./japan_bg.jpg",
    ""
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
      <IconEdit class="text-xl text-white hover:bg-slate-300/20 transition
                duration-300 transform hover:-translate-y-1 hover:scale-125"/>
    </button>
    <transition name="fade">
      <div
          v-if="showModal"
          class="fixed top-0 left-0 w-full h-full bg-black opacity-50 z-10"
          tabindex="0"
          @click="showModal = false"
      ></div>
    </transition>
    <transition name="fade">
      <div
          v-if="showModal"
          class="fixed top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-slate-100/20 rounded-md shadow-lg z-20"
          style="width: 500px;"
      >
        <div class="p-4 flex justify-between items-center">
          <h2 class="text-lg text-white font-bold mb-4">Choose a Background Picture</h2>
          <button class="bg-gray-200 rounded-md p-2 hover:bg-gray-300" @click="showModal = false">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24"
                 stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
            </svg>
          </button>
        </div>
        <div class="p-4">
          <div class="flex flex-wrap gap-4">
            <div
                v-for="(picture, index) in pictures"
                :key="index"
                class="w-1/4 cursor-pointer transition
                duration-300 transform hover:-translate-y-1 hover:scale-125"
                @click="selectPicture(picture)"
            >
              <img :src="picture" class="w-full rounded-md shadow-md"/>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
