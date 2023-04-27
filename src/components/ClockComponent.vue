<script setup lang="ts">
import {ref} from "vue";
import {format} from "date-fns";

const time = ref("")
const date = ref("")

setInterval(() => {
  const now = new Date()
  time.value = format(now, "hh:mm a")
  date.value = format(now, "MMMM dd, yyyy")
}, 0)
</script>

<template>
  <div class="flex flex-col font-sans tracking-tight items-center text-9xl font-bold text-white ">
    <div class="mb-2">{{ time }}</div>
    <div class="text-2xl font-normal tracking-wide">{{ date }}</div>
  </div>
</template>