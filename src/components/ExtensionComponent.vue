<script setup lang="ts">
import uniqid from "uniqid"
import {reactive, ref, watch, toRaw, onMounted, computed, onUnmounted} from "vue";
import IconCheckboxBlankOutline from '~icons/mdi/checkbox-blank-outline';
import IconCheckboxMarked from '~icons/mdi/checkbox-marked';
import IconTrashCan from '~icons/mdi/trash-can';
import IconEdit from '~icons/mdi/edit';
import ClockComponent from "./ClockComponent.vue";
import SearchComponent from "./SearchComponent.vue";
import LinksComponent from "./LinksComponent.vue";
import {format} from 'date-fns'
import draggable from "vuedraggable";

const isOpen = ref(false);
const showDropdown = ref(false)

components: {
  ClockComponent;
  SearchComponent;
  LinksComponent
}

const drag = ref(false);
const defaultData = {
  columns: [
    {
      name: "Today",
      list: []
    },
    {
      name: "Week",
      list: []
    },
    {
      name: "Month",
      list: []
    }
  ]
} as { columns: Column[] };

interface Column {
  name: String;
  list: {
    id: string;
    description: string;
    state: "new" | "done";
    editing?: boolean;
    addPriority?: boolean;
    categories: string[];
    priority: number;
  }
}

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
    localStorage.setItem("todofy-data", JSON.stringify(newData));
    localStorage.setItem("totalTasks", totalTasks.value);
  }
});

const columns = ref(defaultData.columns)

const totalTasks = computed(() => {
  return columns.value.reduce((total, column) => total + column.list.length, 0);
  localStorage.getItem('totalTasks')
})

const addItem = (event: SubmitEvent, column: Column) => {
  const form = event.target as HTMLFormElement;
  const formData = new FormData(event.target as HTMLFormElement);
  const description = formData.get("description") as string;
  const priority = parseInt(formData.get("priority") as string);

  column.list.push({
    id: uniqid,
    description,
    state: "new"
  });

  form.reset();
};

const editItem = (event: Event, column: Column, listIndex: number) => {
  const form = event.target as HTMLFormElement;
  const formData = new FormData(event.target as HTMLFormElement);
  const description = formData.get("description") as string;
  const priority = parseInt(formData.get("priority") as string);

  column.list[listIndex].description = description;
  column.list[listIndex].priority = priority;
  column.list[listIndex].editing = false;

  form.reset();
};

const deletedTasks = ref(parseInt(localStorage.getItem("deletedTasks")) || 0);

const deleteItem = (column: Column, listIndex: number) => {
  column.list.splice(listIndex, 1);
  deletedTasks.value += 1;
  localStorage.setItem("deletedTasks", deletedTasks.value);
};

const handleStorageChange = () => {
  deletedTasks.value = parseInt(localStorage.getItem('deletedTasks')) || 0;
};

onMounted(() => {
  window.addEventListener('storage', handleStorageChange);
  if (typeof localStorage === "undefined" || localStorage === null) {
    console.log("localStorage is not available");
  } else {
    console.log("localStorage is available");
    try {
      localStorage.setItem("totalTasks", totalTasks.value);
    } catch (error) {
      console.log("Failed to save totalItems to localStorage:", error);
    }
  }
});

onUnmounted(() => {
  window.removeEventListener('storage', handleStorageChange);
});
</script>

<template>
  <div class="font-sans tracking-tight text-xl font-thin text-white">
    <p class="mt-14 mr-14 absolute top-0 right-0">
      Total : {{ totalTasks }}
      <br/>
      Completed: {{ deletedTasks }}
    </p>
  </div>
  <div class="mt-28">
    <ClockComponent />
  </div>
  <div>
    <SearchComponent />
  </div>
  <div  class="mt-14 mr-14 absolute top-0 right-0">
    <LinksComponent />
  </div>
  <div>
    <div class="flex flex-row justify-center space-x-5 pt-10">
      <div
          v-for="column of data.columns"
          :key="column.name"
          class="rounded-md mt-20 bg-slate-200/0 py-4 px-4 ga w-1/4 space-y-5">
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
              class="space-y-4 max-h-44 overflow-y-scroll"
              :class="{
        'min-h-[40px] bg-slate-200/40 rounded-md py-3' : drag,
      }"
              @start="drag = true"
              @end="drag = false"
              style="overflow-y: auto;"
          >
            <template #item="{ element: item, index: listIndex }">
              <div
                  class="group bg-slate-50 rounded-md py-2 px-3 shadow-md flex flex-row space-x-2 items-start relative bg-slate-50/80"
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
                        <IconEdit
                            class="transition-all delay-150 transform hover:-translate-y-1 hover:scale-110 leading-extra-loose"/>
                      </button>
                      <button @click="deleteItem(column, listIndex)">
                        <IconTrashCan
                            class="transition-all delay-150 transform hover:-translate-y-1 hover:scale-110 leading-extra-loose text-red-700"/>
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
  </div>
</template>

<style scoped>
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
  @apply opacity-50 bg-gray-100 border border-blue-500 bg-slate-100 opacity-40;
}

.list-enter,
.list-leave-to {
  opacity: 0;
}

.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.3s ease;
}

.slide-up-enter,
.slide-up-leave-to {
  transform: translateY(100%);
  opacity: 0;
}
</style>
