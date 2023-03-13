<script setup lang="ts">
import uniqid from "uniqid"
import {reactive, ref, watch} from "vue";
import IconCheckboxBlankOutline from '~icons/mdi/checkbox-blank-outline'
import IconCheckboxMarked from '~icons/mdi/checkbox-marked'
import IconTrashCan from '~icons/mdi/trash-can'
import IconEdit from '~icons/mdi/edit'
import draggable from "vuedraggable";


const drag = ref(false);
const defaultData = {
  columns: [
    {
      name: "now",
      list: []
    },
    {
      name: "next",
      list: []
    },
    {
      name: "later",
      list: []
    }
  ]
} as { columns: Column[] }

const localStorageString = localStorage.getItem("toodaloo-app-data");
const localStorageData = localStorageString ? JSON.parse(localStorageString) : defaultData;

const data = reactive(localStorageData);

watch(data, (newData) => {
  localStorage.setItem("toodaloo-app-data", JSON.stringify(newData))
});

interface Column {
  name: string;
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
  <div class="flex flex-row justify-center space-x-5 pt-10">
    <div
        v-for="column of data.columns"
        :key="column.name"
        class="rounded-md shadow-md bg-slate-100 py-4 px-4 ga w-1/4 space-y-5">
      <div class="mb-3">{{ column.name }}</div>
      <draggable
          v-model="column.list"
          group="items"
          item-key="id"
          class="space-y-4"
          :class="{
            'min-h-[40px] bg-slate-200 rounded-md py-3' : drag,
          }"
          @start="drag = true"
          @end="drag = false"
          ghost-class="ghost"
      >
        <template #item="{ element: item, index: listIndex }">
          <div
              class="group bg-slate-50 rounded-md py-2 px-3 shadow-md flex flex-row space-x-2 items-start relative"
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
                    :value="item.description"
                    class="w-full px-3 py-2 rounded-md mb-2"
                    autocomplete="off"
                />
                <div class="flex justify-between">
                  <button type="button"
                          @click="item.editing = false"
                          class="text-xs"
                  >
                    Cancel
                  </button>
                  <button>Add</button>
                </div>
              </form>
            </template>
            <template v-else>
              <div
                  class="absolute hidden right-0 bottom-0 p-3 bg-slate-50 opacity-90 max-h-full space-x-3 flex-row group-hover:flex"
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
      <div class="pt-4">
        <form @submit.prevent="addItem($event, column)" class="flex flex-col justify-end">
          <input
              type="text"
              name="description"
              class="w-full px-3 py-2 rounded-md mb-2"
              autocomplete="off"
          />
          <button>Add</button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.ghost {
  @apply bg-slate-400 opacity-40;
}
</style>
