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

const showDropDown = ref(false);
const showAddLinkForm = ref(false);
const showEditLinkForm = ref(-1);
const editName = ref("");
const editUrl = ref("");

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

const deleteLink = (index: number) => {
  links.value.splice(index, 1);
};

const onFormSubmit = async (event: Event) => {
  event.preventDefault(); // prevent the default form submission behavior

  if (!newLinkName.value || !newLinkUrl.value) {
    return;
  }

  if (showEditLinkForm.value >= 0) {
    await updateLink(showEditLinkForm.value); // wait for the link to be updated
    showEditLinkForm.value = -1; // hide the form
  } else {
    await addLink(); // wait for the new link to be added
    showAddLinkForm.value = false; // hide the form
  }
};

const onEditFormSubmit = (event: Event, index: number) => {
  event.preventDefault(); // prevent the default form submission behavior

  if (!editName.value || !editUrl.value) {
    return;
  }

  links.value[index].name = editName.value;
  links.value[index].url = editUrl.value;

  showEditLinkForm.value = -1; // hide the form
};

const editLink = (index: number) => {
  const link = links.value[index];
  editName.value = link.name;
  editUrl.value = link.url;
  showEditLinkForm.value = index;
};

const cancelEdit = () => {
  showEditLinkForm.value = -1;
};

const newLinkName = ref("");
const newLinkUrl = ref("");
</script>

<template>
    <div class="relative">
      <div class="relative ml-4">
        <button
            type="button"
            @click="showDropDown = !showDropDown"
            :class="{'bg-gray-100 hover:bg-gray-200': showDropDown, 'bg-white': !showDropDown}"
            class="px-2 py-1 rounded border"
        >
          Links
        </button>
        <ul v-show="showDropDown" @click.away="showDropDown = false" class="absolute z-10 bg-white py-2 mt-2 w-64 rounded-lg shadow-lg" style="right: 0;">
          <li v-for="(link, index) in links" :key="index" class="px-4 py-2">
            <a :href="link.url" target="_blank" rel="noopener noreferrer" class="text-blue-500">{{ link.name }}</a>
            <button @click.stop="editLink(index)" class="ml-2 text-gray-500 hover:text-gray-700">Edit</button>
            <button @click.stop="deleteLink(index)" class="ml-2 text-gray-500 hover:text-gray-700">Delete</button>
          </li>
          <li class="border-t border-gray-200 px-4 py-2">
            <button
                type="button"
                @click.stop="showAddLinkForm = !showAddLinkForm"
                class="text-gray-500 hover:text-gray-700"
            >
              + New Link
            </button>
          </li>
        </ul>
      </div>

    <div class="relative">
      <form
          v-show="showAddLinkForm || showEditLinkForm >= 0"
          @submit.prevent="showEditLinkForm >= 0 ? onEditFormSubmit($event, showEditLinkForm) : onFormSubmit($event)"
          class="absolute top-0 right-0 mt-2 mr-2 w-64 bg-white rounded-lg p-4 shadow-lg z-10"
      >
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
          <button
              class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors"
              type="submit"
          >
            {{ showEditLinkForm >= 0 ? 'Save' : 'Add Link' }}
          </button>
          <button
              class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600 transition-colors ml-4"
              type="button"
              @click="showAddLinkForm = false; showEditLinkForm = -1"
          >
            Cancel
          </button>
        </div>
      </form>
    </div>
  </div>
</template>