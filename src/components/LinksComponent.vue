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

const editLink = (index) => {
  const link = links.value[index];
  newLinkName.value = link.name;
  newLinkUrl.value = link.url;
  showAddLinkForm.value = true;
}

</script>

<template>
  <div class="relative">
    <button
        type="button"
        @click.stop="showAddLinkForm = !showAddLinkForm"
        class="bg-gray-100 hover:bg-gray-200 px-2 py-1 rounded border"
    >
      + New Link
    </button>

    <ul class="ml-4">
      <li v-for="(link, index) in links" :key="index" class="pt-2 flex items-center space-x-4">
        <a :href="link.url" class="text-blue-500">{{ link.name }}</a>
        <button type="button" @click="editLink(index)" class="text-gray-500">
          Edit
        </button>
        <button type="button" @click="removeLink(index)" class="ml-4 text-red-500">
          Remove
        </button>
      </li>
    </ul>

    <div class="relative">
      <form v-show="showAddLinkForm || showEditLinkForm" @submit.prevent="onFormSubmit" class="absolute top-0 right-0 mt-2 mr-2 w-64 bg-white rounded-lg p-4 shadow-lg z-10">
        <div class="mb-4">
          <label class="block text-gray-700 font-bold mb-2" for="new-link-name">Name:</label>
          <input
              class="appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              id="new-link-name"
              v-model="newLinkName"
              type="text"
              required
              @click.stop
          />
        </div>
        <div class="mb-4">
          <label class="block text-gray-700 font-bold mb-2" for="new-link-url">URL:</label>
          <input
              class="appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              id="new-link-url"
              v-model="newLinkUrl"
              type="url"
              required
              @click.stop
          />
        </div>
        <div class="flex justify-end">
          <button class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors" type="submit">{{ showAddLinkForm ? 'Add Link' : 'Save' }}</button>
          <button v-if="showEditLinkForm" class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600 transition-colors ml-4" type="button" @click="cancelEdit">Cancel</button>
        </div>
      </form>
      <div v-show="showAddLinkForm || showEditLinkForm" class="fixed top-0 left-0 right-0 bottom-0 bg-gray-900 opacity-50 z-0" @click="showAddLinkForm = false; showEditLinkForm = false"></div>
    </div>
  </div>
</template>
