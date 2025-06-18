<script setup lang="ts">
import TrelloBoard from "./components/TrelloBoard.vue";
</script>

<template>
  <div class="h-screen overflow-auto bg-teal-600 p-10">
    <h1 class="mb-10 flex items-center gap-2 text-3xl font-semibold text-white">
      <img
        src="/public/img/vueschool-logo.svg"
        alt="Vue School Logo"
        width="200"
      />
      Trello Alike Board
    </h1>
    <TrelloBoard />
  </div>
</template>
