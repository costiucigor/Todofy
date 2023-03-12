<script setup lang="ts">
import uniqid from "uniqid"
import {reactive, watch} from "vue";

  const defaultData = {
    columns: [
      {
        name: "now",
        list: [
        ]
      },
      {
        name: "next",
        list: [
        ]
      },
      {
        name: "later",
        list: [
        ]
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
  <div class="flex flex-row justify-center space-x-4">
    <div
        v-for="column of data.columns"
        :key="column.name"
        class="rounded-md shadow-md bg-slate-100 py-4 px-4 w-1/4 space-y-5">
      <div>{{ column.name }}</div>
      <div
           v-for="item of column.list"
           :key="item.id"
           class="bg-slate-50 rounded-md py-2 px-3 shadow-md">
        {{ item.description }}
      </div>
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
