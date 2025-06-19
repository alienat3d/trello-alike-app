<script setup lang="ts">
import type { Task } from "~/types";

defineProps<{
  task: Task;
}>();
</script>

<template>
  <div
    :title="task.createdAt.toLocaleDateString()"
    class="task mb-2 max-w-[250px] rounded bg-white p-2 shadow-sm"
  >
    <DragHandle /><span>{{ task.title }}</span>
  </div>
</template>
