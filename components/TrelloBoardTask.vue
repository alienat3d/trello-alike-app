<script setup lang="ts">
import type { Task, ID } from "~/types";

const props = defineProps<{
  task: Task;
}>();

const emit = defineEmits<{
  (e: "delete", payload: ID): void;
}>();

const focused = ref(false);

onKeyStroke("Backspace", (e) => {
  if (focused.value) emit("delete", props.task.id);
});
</script>

<template>
  <div
    @focus="focused = true"
    @blur="focused = false"
    :title="task.createdAt.toLocaleDateString()"
    class="task mb-2 max-w-[250px] rounded bg-white p-2 shadow-sm"
    tabindex="0"
  >
    <DragHandle /><span>{{ task.title }}</span>
  </div>
</template>

<style>
@import "tailwindcss";

.sortable-drag .task {
  transform: rotate(4deg);
}

.sortable-ghost .task {
  @apply relative;
}
.sortable-ghost .task::after {
  content: "";
  @apply absolute top-0 right-0 bottom-0 left-0 rounded bg-slate-300;
}

.task:focus,
.task:focus-visible {
  @apply !outline-gray-400;
  outline: gray auto 1px;
}
</style>
