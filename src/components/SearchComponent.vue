<script setup lang="ts">
import {ref} from "vue";

const query = ref("")

const search = () => {
  window.open(`https://www.google.com/search?q=${encodeURIComponent(query.value)}`, '_blank')
}
</script>

<template>
  <div>
    <form @submit.prevent="search">
      <input type="text" v-model="query">
      <button type="submit">Search</button>
    </form>
  </div>
</template>
