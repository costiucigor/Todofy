<script setup lang="ts">
import { ref } from "vue";

const query = ref("");
const showSearch = ref(false);
const isFocused = ref(false);

const search = () => {
  window.open(`https://www.google.com/search?q=${encodeURIComponent(query.value)}`, '_blank')
}
</script>

<template>
  <form @submit.prevent="search" class="relative mx-auto w-max right-0">
    <input type="search"
           v-model="query"
           class="peer cursor-pointer relative z-10 h-12 w-12 bg-transparent pl-12 outline-none focus:w-full focus:cursor-text focus:border-b-cyan-800 focus:pl-16 focus:pr-4" />
    <svg xmlns="http://www.w3.org/2000/svg" class="absolute inset-y-0 my-auto h-8 w-12 border-r border-transparent stroke-white px-3.5 peer-focus:border-cian-300 peer-focus:stroke-cian-500 text-black" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
      <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
    </svg>
  </form>
</template>
