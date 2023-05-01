<script setup lang="ts">
 import {ref} from "vue";

 interface Link {
   name: String;
   url: String;
 }

 const links = ref<Link[]>([
     {name: "Google tab", url: "#"},
     {name: "About", url: "#"},
 ]);

 const isOpen = ref(false);
 const showAddLinkForm = ref(false);

 const newLinkName = ref("");
 const newLinkUrl = ref("");

 const addLink = () => {
   if (!newLinkName.value || !newLinkUrl.value) {
     return;
   }

   links.value.push({
     name: newLinkName.value,
     url: newLinkUrl.value,
   });

   newLinkName.value = "";
   newLinkUrl.value = "";
 }

 const removeLink = (index: number) => {
   links.value.splice(index, 1);
 }
</script>

<template>
  <div class="relative" @click="isOpen = !isOpen">
    <button
        type="button"
        class="inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
    >
      Links
      <svg
          class="-mr-1 ml-2 h-5 w-5"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
          aria-hidden="true"
      >
        <path
            fill-rule="evenodd"
            d="M10.293 14.707a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L10 12.586l3.293-3.293a1 1 0 011.414 1.414l-4 4z"
            clip-rule="evenodd"
        />
      </svg>
    </button>
    <div
        v-if="isOpen"
        class="origin-top-right absolute right-0 h-56 mt-2 w-56 rounded-md shadow-lg bg-white ring-1 ring-black ring-opacity-5"
    >
      <div class="py-1" role="menu" aria-orientation="vertical" aria-labelledby="options-menu">
        <div class="relative">
          <button
              type="button"
              @click="showAddLinkForm = !showAddLinkForm"
              class="bg-gray-100 hover:bg-gray-200 px-2 py-1 rounded border"
          >
            + New Link
          </button>
          <form v-show="showAddLinkForm" @submit.prevent="addLink" class="absolute mt-2 w-64">
            <div class="flex space-x-2">
              <div class="w-1/2">
                <label for="new-link-name">Name:</label>
                <input id="new-link-name" v-model="newLinkName" type="text" required />
              </div>
              <div class="w-1/2">
                <label for="new-link-url">URL:</label>
                <input id="new-link-url" v-model="newLinkUrl" type="url" required />
              </div>
            </div>
            <div class="flex justify-end">
              <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-2 rounded">
                Add Link
              </button>
            </div>
          </form>
          <ul>
            <li v-for="(link, index) in links" :key="index">
              <a :href="link.url">{{ link.name }}</a>
              <button type="button" @click="removeLink(index)">
                Remove
              </button>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>