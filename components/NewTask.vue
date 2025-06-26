<script setup lang="ts">
import type { Task } from "~/types";
import { nanoid } from "nanoid";

const emit = defineEmits<{
  (e: "add", payload: Task): void;
}>();

const focused = ref(false);
const title = ref("");

function createTask(e: Event) {
  if (title.value.trim()) {
    e.preventDefault();
    emit("add", {
      title: title.value.trim(),
      createdAt: new Date(),
      id: nanoid(),
    } as Task);
  }

  title.value = "";
}
</script>

<template>
  <div>
    <textarea
      v-model="title"
      @focus="focused = true"
      @blur="focused = false"
      @keydown.tab="createTask"
      @keyup.enter="createTask"
      class="w-full cursor-pointer resize-none overflow-y-hidden rounded border-none p-2 focus:bg-white focus:shadow"
      :class="{ 'h-7': !focused, 'h-20': focused }"
      style="outline: none !important"
      :placeholder="!focused ? '+ Add a card' : 'Enter a title for this card'"
    ></textarea>
  </div>
</template>
