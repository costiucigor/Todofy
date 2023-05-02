<script setup lang="ts">
import { ref } from "vue";

interface Link {
  name: string;
  url: string;
}

const links = ref<Link[]>([
  { name: "Google", url: "https://www.google.com" },
  { name: "GitHub", url: "https://github.com" }
]);

const isOpen = ref(false);
const showAddLinkForm = ref(false);
const showEditLinkForm = ref(-1);

const newLinkName = ref("");
const newLinkUrl = ref("");

const addLink = () => {
  if (!newLinkName.value || !newLinkUrl.value) {
    return;
  }

  links.value.push({
    name: newLinkName.value,
    url: newLinkUrl.value
  });

  newLinkName.value = "";
  newLinkUrl.value = "";

  if (showAddLinkForm.value) {
    showAddLinkForm.value = false;
  }
};

const removeLink = (index: number) => {
  links.value.splice(index, 1);
};

const onFormSubmit = (event: Event) => {
  event.preventDefault(); // prevent the default form submission behavior

  if (!newLinkName.value || !newLinkUrl.value) {
    return;
  }

  addLink(); // call the addLink function to add the new link
  showAddLinkForm.value = false; // hide the form
};

const onEditFormSubmit = (event: Event, index: number) => {
  event.preventDefault(); // prevent the default form submission behavior

  if (!editName[index] || !editUrl[index]) {
    return;
  }

  links.value[index].name = editName[index];
  links.value[index].url = editUrl[index];

  showEditLinkForm.value = -1;
};

const editName = ref<string[]>([]);
const editUrl = ref<string[]>([]);

const startEditingLink = (index: number) => {
  editName.value[index] = links.value[index].name;
  editUrl.value[index] = links.value[index].url;

  showEditLinkForm.value = index;
};

const cancelEditingLink = (index: number) => {
  editName.value[index] = "";
  editUrl.value[index] = "";

  showEditLinkForm.value = -1;
};
</script>

<template>
  <div class="relative">
    <button
        type="button"
        class="inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
        @click="isOpen = !isOpen"
        aria-haspopup="true"
        :aria-expanded="isOpen"
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
        class="origin-top-left absolute left-0 h-56 mt-2 w-56 rounded-md shadow-lg bg-white ring-1 ring-black ring-opacity-5"
        @click.away="isOpen = false"
        @keydown.escape="isOpen = false"
        aria-labelledby="options-menu"
        role="menu"
    >
      <div class="py-1">
        <ul class="ml-4">
          <li v-for="(link, index) in links" class="pt-" :key="index">
            <a :href="link.url">{{ link.name }}</a>
            <button type="button" @click="removeLink(index)" class="ml-4 text-red-500">
              Remove
            </button>
          </li>
        </ul>
        <button
            type="button"
            @click.stop="showAddLinkForm = !showAddLinkForm"
            class="bg-gray-100 hover:bg-gray-200 px-2 py-1 rounded border"
        >
          + New Link
        </button>
      </div>
    </div>
    <form
        v-show="showAddLinkForm"
        @submit.prevent="onFormSubmit"
        class="absolute mt-2 w-64"
    >
      <div class="flex space-x-2">
        <div class="w-1/2">
          <label for="new-link-name">Name:</label>
          <input
              id="new-link-name"
              v-model="newLinkName"
              type="text"
              required
              @click.stop
          />
        </div>
        <div class="w-1/2">
          <label for="new-link-url">URL:</label>
          <input
              id="new-link-url"
              v-model="newLinkUrl"
              type="url"
              required
              @click.stop
          />
        </div>
      </div>
      <div class="flex justify-end">
        <button class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors" type="submit">Add Link</button>
      </div>
    </form>

  </div>
</template>
