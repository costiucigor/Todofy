<script setup lang="ts">
import uniqid from "uniqid"
import {reactive, ref, watch} from "vue";
import IconCheckboxBlankOutline from '~icons/mdi/checkbox-blank-outline'
import IconCheckboxMarked from '~icons/mdi/checkbox-marked'
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
        <template #item="{ element: item }">
          <div
              class="bg-slate-50 rounded-md py-2 px-3 shadow-md flex flex-row space-x-2 items-start">
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
