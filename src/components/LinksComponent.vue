<script setup lang="ts">
import {computed, ref} from "vue";

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
  if (!currentLinkName.value || !currentLinkUrl.value) {
    return;
  }

  links.value.push({
    name: currentLinkName.value,
    url: currentLinkUrl.value
  });

  newLinkName.value = "";
  newLinkUrl.value = "";

  if (showAddLinkForm.value) {
    showAddLinkForm.value = false;
  }

  saveLinksToStorage();
};

const deleteLink = (index: number) => {
  links.value.splice(index, 1);
  saveLinksToStorage();
};

const updateLink = (index: number) => {
  const linkToUpdate = links.value[index];
  // perform some update operation on linkToUpdate, for example:
  linkToUpdate.name = editName.value;
  linkToUpdate.url = editUrl.value;
  saveLinksToStorage();
};

const onFormSubmit = (event: Event) => {
  event.preventDefault();

  if (!newLinkName.value || !newLinkUrl.value) {
    return;
  }

  if (showEditLinkForm.value >= 0) {
    updateLink(showEditLinkForm.value);
  } else {
    links.value.push({
      name: newLinkName.value,
      url: newLinkUrl.value
    });
    saveLinksToStorage();
  }

  newLinkName.value = "";
  newLinkUrl.value = "";
  showAddLinkForm.value = false;
  showEditLinkForm.value = -1;
};

const onEditFormSubmit = (event: Event, index: number) => {
  event.preventDefault(); // prevent the default form submission behavior

  updateLink(index);
  showEditLinkForm.value = -1; // hide the form
};

const editLink = (index: number) => {
  const link = links.value[index];
  editName.value = link.name;
  editUrl.value = link.url;
  showEditLinkForm.value = index;
};

const saveLinksToStorage = () => {
  const serializedLinks = JSON.stringify(links.value);
  localStorage.setItem("links", serializedLinks);
};

const currentLinkName = computed(() => {
  return showEditLinkForm.value >= 0 ? editName.value : newLinkName.value;
});

const currentLinkUrl = computed(() => {
  return showEditLinkForm.value >= 0 ? editUrl.value : newLinkUrl.value;
});

const newLinkName = ref("");
const newLinkUrl = ref("");

</script>

<template>
  <div class="relative">
    <div class="relative ml-4">
      <button
          type="button"
          @click="showDropDown = !showDropDown"
          :class="{'text-black border-black': showDropDown, 'text-white': !showDropDown}"
          class="px-2 py-1 rounded border"
      >
        Links
      </button>
      <ul v-show="showDropDown" @click.away.stop="showDropDown = false" class="absolute z-10 bg-white py-2 mt-2 w-64 rounded-lg shadow-lg" style="right: 0;">
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
          <div v-if="showEditLinkForm >= 0 || showAddLinkForm" class="mb-4">
            <label class="block text-gray-700 font-bold mb-2" for="edit-link-name">Name:</label>
            <input
                class="appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                id="edit-link-name"
                :value="currentLinkName"
                @input="showEditLinkForm >= 0 ? editName = $event.target.value : newLinkName = $event.target.value"
                type="text"
                required
                @click.stop
            />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2" for="edit-link-url">URL:</label>
            <input
                class="appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                id="edit-link-url"
                :value="currentLinkUrl"
                type="text"
                required
                @click.stop
            />
          </div>
          <div class="flex justify-end">
            <button
                type="button"
                @click.stop="showAddLinkForm = false; showEditLinkForm = -1"
                class="bg-white text-gray-700 hover:bg-gray-100 py-2 px-4 border border-gray-300 rounded shadow mr-2"
            >
              Cancel
            </button>
            <button
                type="submit"
                :disabled="!newLinkName && !editName || !newLinkUrl && !editUrl"
                class="bg-blue-500 text-white py-2 px-4 rounded shadow"
                @click.prevent="addLink"
            >
              {{ showEditLinkForm >= 0 ? 'Save' : 'Add' }}
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>