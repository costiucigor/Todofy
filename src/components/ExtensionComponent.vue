<script setup lang="ts">
import uniqid from "uniqid"
import {reactive, ref, watch, toRaw} from "vue";
import IconCheckboxBlankOutline from '~icons/mdi/checkbox-blank-outline'
import IconCheckboxMarked from '~icons/mdi/checkbox-marked'
import IconTrashCan from '~icons/mdi/trash-can'
import IconEdit from '~icons/mdi/edit'
import {format} from 'date-fns'
import draggable from "vuedraggable";


const time = ref("")
const date = ref("")
const showDropdown = ref(false)

setInterval(() => {
  const now = new Date()
  time.value = format(now, "hh:mm a")
  date.value = format(now, "MMMM dd, yyyy")
}, 0)

const drag = ref(false);
const defaultData = {
  columns: [
    {
      name: "Next",
      list: []
    },
    {
      name: "Now",
      list: []
    },
    {
      name: "Later",
      list: []
    }
  ]
} as { columns: Column[] }

const data = reactive(defaultData);

if (chrome?.storage) {
  chrome.storage.sync.get(["key"], (result) => {
    const storeData = result["key"];
    if (storeData) {
      data.columns = storeData.columns;
    }
  });
} else {
  const localStorageString = localStorage.getItem("todofy-data");
  if (localStorageString) {
    data.columns = JSON.parse(localStorageString).columns;
  }
}

watch(data, (newData) => {
  if (chrome?.storage) {
    chrome.storage.sync.set({key: toRaw(data)}, () => {
    });
  } else {
    localStorage.setItem("todofy-data", JSON.stringify(newData))
  }
});

interface Column {
  name: String;
  list: {
    id: string;
    description: string;
    state: "new" | "done";
    editing?: boolean;
  }
}

const addItem = (event: SubmitEvent, column: Column) => {
  const form = event.target as HTMLFormElement;
  const formData = new FormData(event.target as HTMLFormElement);
  const description = formData.get("description") as string;
  console.log(description)

  column.list.push({
    id: uniqid,
    description,
    state: "new"
  });

  form.reset();
}

const deleteItem = (column: Column, listIndex: number) => {
  column.list.splice(listIndex, 1);
}

const editItem = (event: Event, column: Column, listIndex: number) => {
  const form = event.target as HTMLFormElement;
  const formData = new FormData(event.target as HTMLFormElement);
  const description = formData.get("description") as string;
  console.log(description)

  column.list[listIndex].description = description
  column.list[listIndex].editing = false;

  form.reset();
}
</script>

<template>
  <div class="flex flex-col font-sans tracking-tight items-center pt-10 text-9xl font-bold text-white ">
    <div class="mb-2 ">{{ time }}</div>
    <div class="text-2xl font-normal tracking-wide">{{ date }}</div>
  </div>
  <div>
  </div>
  <div class="flex flex-row justify-center space-x-5 pt-10">
    <div
        v-for="column of data.columns"
        :key="column.name"
        class="rounded-md mt-40 bg-slate-200/0 py-4 px-4 ga w-1/4 space-y-5">
      <div class="flex justify-center">
        <div class="mb-3 text-xl font-bold text-white">{{ column.name }}</div>
      </div>
      <transition-group name="list" tag="div" mode="out-in">
        <draggable
            v-model="column.list"
            group="items"
            item-key="id"
            :animation="200"
            ghost-class="moving-card"
            class="space-y-4 max-h-46 overflow-y-scroll"
            :class="{
        'min-h-[40px] bg-slate-200/40 rounded-md py-3' : drag,
      }"
            @start="drag = true"
            @end="drag = false"
            style="overflow-y: auto;"
            ghost-class="ghost"
        >
          <template #item="{ element: item, index: listIndex }">
            <div
                class="group bg-slate-50 rounded-md py-2 px-3 shadow-md flex flex-row space-x-2 items-start relative bg-slate-50/40"
                :key="item.id"
                v-bind:class="{ 'list-move': true }"
            >
            <template v-if="item.editing">
              <form
                  v-if="item.editing"
                  @submit.prevent="editItem($event, column, listIndex)"
                  class="flex flex-col w-full"
              >
                <input
                    type="text"
                    name="description"
                    v-model="item.description"
                    class="w-full px-3 py-2 rounded-md mb-2"
                    autocomplete="on"
                    required
                />
                <div class="flex justify-between">
                  <button type="button"
                          @click="item.editing = false"
                          class="bg-red-700 mb-4 rounded-md text-white text-l hover:bg-red-500 font-heavy w-20 transition duration-300 transform hover:-translate-y-1 hover:scale-110 leading-extra-loose"
                  >
                    Cancel
                  </button>
                  <button
                      class="bg-green-700 mb-4 rounded-md text-white text-l hover:bg-green-500 font-heavy w-20 transition duration-300 transform hover:-translate-y-1 hover:scale-110 leading-extra-loose">
                    Save
                  </button>
                </div>
              </form>
            </template>
            <template v-else>
              <div
                  class="absolute hidden right-0 bottom-0 p-3 bg-slate-50/0 opacity-90 max-h-full space-x-3 flex-row group-hover:flex"
              >
                <div>
                  <button @click="item.editing = true">
                    <IconEdit/>
                  </button>
                  <button @click="deleteItem(column, listIndex)">
                    <IconTrashCan class="text-red-700"/>
                  </button>
                </div>
              </div>
              <button
                  class="mt-[2px]"
                  @click="item.state = item.state === 'done' ? 'new' : 'done'"
              >
                <IconCheckboxBlankOutline v-if="item.state !== 'done'"/>
                <IconCheckboxMarked v-else/>
              </button>
              <span
                  class="inline-block break-words min-w-0"
                  :class="{
                'line-through': item.state === 'done',
        }"
              >
           {{ item.description }}
        </span>
            </template>
          </div>
        </template>
      </draggable>
      </transition-group>
      <div class="pt-4">
        <form @submit.prevent="addItem($event, column)" class="flex flex-col justify-end">
          <input
              type="text"
              name="description"
              class="w-full px-3 py-2 rounded-md mb-2 bg-slate-50/30"
              autocomplete="off"
              required
          />
          <div class="flex justify-center">
            <button
                class="bg-slate-500/30 mb-4 rounded-md text-white text-l font-slim hover:bg-slate-400 subpixel-antialiased w-20 transition
                duration-300 transform hover:-translate-y-1 hover:scale-110 leading-extra-loose">
              Add
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.ghost {
  @apply bg-slate-100 opacity-40;
}

::-webkit-scrollbar {
  width: 6px;
  border-radius: 3px;
}

::-webkit-scrollbar-track {
  border-radius: 3px;
}

::-webkit-scrollbar-thumb {
  background-color: #888;
  border-radius: 3px;
}

.list-enter-active,
.list-leave-active {
  transition: opacity 5s ease-out;
}

.moving-card {
  @apply opacity-50 bg-gray-100 border border-blue-500;
}

.list-enter,
.list-leave-to {
  opacity: 0;
}
</style>
