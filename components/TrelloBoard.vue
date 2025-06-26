<script setup lang="ts">
import type { Column, Task } from "~/types";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";
import TrelloBoardTask from "./TrelloBoardTask.vue";
import NewTask from "./NewTask.vue";

const columns = useLocalStorage<Column[]>("trelloBoard", [
  {
    title: "Backlog",
    id: nanoid(),
    tasks: [
      {
        title: "Create a landing page",
        createdAt: new Date(),
        id: nanoid(),
      },
      {
        title: "Develop a new feature",
        createdAt: new Date(),
        id: nanoid(),
      },
      {
        title: "Fix the page navigation bug",
        createdAt: new Date(),
        id: nanoid(),
      },
    ],
  },
  { title: "Selected for Dev", id: nanoid(), tasks: [] },
  { title: "In Progress", id: nanoid(), tasks: [] },
  { title: "QA", id: nanoid(), tasks: [] },
  { title: "Complete", id: nanoid(), tasks: [] },
]);

const isAltPressed = useKeyModifier("Alt");

const createColumn = () => {
  const column: Column = {
    id: nanoid(),
    title: "",
    tasks: [],
  };

  columns.value.push(column);

  nextTick(() =>
    (
      document.querySelector(
        ".column:last-of-type .title-input",
      ) as HTMLInputElement
    )?.focus(),
  );
};
</script>

<template>
  <div class="flex items-start gap-4 overflow-x-auto">
    <draggable
      v-model="columns"
      group="columns"
      item-key="id"
      :animation="150"
      handle=".drag-handle"
      class="flex items-start gap-4 pb-10"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column min-w-[250px] rounded bg-gray-200 p-5">
          <header class="mb-4 font-bold">
            <DragHandle />
            <input
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? (columns = columns.filter((c) => c.id !== column.id))
                  : null
              "
              v-model="column.title"
              class="title-input w-4/5 rounded bg-transparent px-1 focus:bg-white"
              type="text"
            />
          </header>
          <draggable
            v-model="column.tasks"
            :group="{ name: 'tasks', pull: isAltPressed ? 'clone' : true }"
            item-key="id"
            handle=".drag-handle"
            :animation="150"
          >
            <template #item="{ element: task }: { element: Task }">
              <div>
                <TrelloBoardTask
                  :task="task"
                  @delete="
                    column.tasks = column.tasks.filter((t) => t.id !== $event)
                  "
                />
              </div>
            </template>
          </draggable>
          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      @click="createColumn"
      class="min-w-[250px] cursor-pointer rounded bg-gray-200 p-4 text-left whitespace-nowrap opacity-50"
    >
      + Add another column
    </button>
  </div>
</template>
