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

<!-- 12.2 Чтобы избавиться от ошибки, связанной с датой создания задачи, после подключения функции сохранения в Local Storage поменяем строчку ':title="task.createdAt.toLocaleDateString()' на ':title="new Date(task.createdAt).toLocaleDateString()"' -->
<!-- ? 12.3 Как опция может быть добавлено время в другом формате с помощью либы «date-fns»: 

import {format} from 'date-fns'

:title="format(new Date(task.createdAt), 'dd/MM/yyyy')" (иной формат времени) -->
<template>
  <div
    @focus="focused = true"
    @blur="focused = false"
    :title="new Date(task.createdAt).toLocaleDateString()"
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
