<script setup lang="ts">
import type { Column, Task } from "~/types";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";
import TrelloBoardTask from "./TrelloBoardTask.vue";

const columns = ref<Column[]>([
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

// 7.1 Для отслеживания нажатия клавиш на клавиатуре будем использовать удобную composable-функцию "useKeyModifier" и передадим в неё строкой название клавиши, нажатие которой мы хотим отслеживать. Она будет возвращать булево значение в переменную, в зависимости от того, нажата клавиша или нет.
const isAltPressed = useKeyModifier("Alt");
</script>

<template>
  <div>
    <draggable
      v-model="columns"
      group="columns"
      item-key="id"
      :animation="150"
      handle=".drag-handle"
      class="flex items-start gap-4 overflow-x-auto pb-10"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column min-w-[250px] rounded bg-gray-200 p-5">
          <header class="mb-4 font-bold">
            <DragHandle />{{ column.title }}
          </header>
          <!-- * 6.0 Добавить такую же функцию перетаскивания для задач будет также легко, как и с колонками, надо всего лишь скопировать оттуда комп. "draggable" и обернуть им наш комп. "TrelloBoardTask". Однако конечно придётся внести также кое какие изменения, например в "v-model" у нас теперь попадает "column.tasks", а в group - "tasks". Это нужно, чтобы различать их с другим "draggable" компонентом. Чтобы было невозможно перенести элементы задач в массив с колонками и обратно. Т.к. мы хотим, чтобы задачи можно было перемещать только внутри своей колонки или в другую колонку к задачам. Ещё одно преимущество заключается в том, что этот "draggable" компонент существует более, чем в одном экземпляре, т.к. создаётся для каждой колонки и т.к. у всех будет группа "tasks", то можно будет перемещать задачи между этими группами в разных колонках. Т.е. переносить одну задачу из одного списка задач в список из другой колонки. -->
          <!-- 6.1 А ещё мы удалим классы стилизации, т.к. тут они уже не нужны. -->
          <!-- * 7.0 В документации к "SortableJS", на которой базируется наша библиотека "Vue Draggable", можно найти, что мы можем добавлять разные принципы работы при перетаскивании элементов, например, вместо "drag & drop" может быть также "clone". Итак, сделаем значение "group" объектом, в котором будет уже свойство "name" со значением "tasks", а также свойство "pull" со значением "clone" включающий режим клонирования. Однако, нам бы хотелось включать этот режим только, когда зажата клавиша "Alt". ↑ -->
          <draggable
            v-model="column.tasks"
            :group="{ name: 'tasks', pull: isAltPressed ? 'clone' : true }"
            item-key="id"
            handle=".drag-handle"
            :animation="150"
          >
            <!-- 6.2 Также, как мы и делали с колонками, поместим их в слот, а данные извлечём из переменной "task" и укажем тип "Task". -->
            <!-- 6.3 Мы также можем избавиться от "v-for" & "key". -->
            <!-- 6.4 Осталось добавит "handle", чтобы перетаскивать задачи можно было лишь по зажатию кнопки мышки в специальной зоне задачи. И также добавим соотв. комп. в [components\TrelloBoardTask.vue]. -->
            <!-- 8.2 Однако, чтобы применить стили нам нужно добавить элементу задач доп. div-обёртку. -->
            <template #item="{ element: task }: { element: Task }">
              <div>
                <TrelloBoardTask :task="task" />
              </div>
            </template>
          </draggable>
          <footer><button class="text-gray-500">+ Add a Card</button></footer>
        </div>
      </template>
    </draggable>
  </div>
</template>

<!-- * 8.0 В оригинальном «Trello» при перетаскивании элементы задач наклоняются в одну сторону, мы можем сделать тоже самое. «SoratableJS» даёт нам три класса для разных состояний: "sortable-chosen" (когда мы только начали перетаскивать элемент кликнув на нём и удерживая кнопку мыши.), "sortable-drag" (когда мы начали движение удерживая элемент) и "sortable-ghost" (применяется к дублирующему полупрозрачному отображению элемента, показывающее где он упадёт, если отпустить кнопку мыши). В нашем примере "sortable-chosen" не пригодится. -->
<!-- 8.1 При перетаскивании элемента мы хотим слегка наклонять вбок элемент задачи наподобие как в оригинальном «Trello». ↑ -->
<!-- 8.3 Теперь займёмся стилизацией "призрака", для достижения эффекта, как у оригинального «Trello» лучше всего подойдёт псевдоэлемент. -->
<style>
@import "tailwindcss";

/* .sortable-chosen {} */

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
</style>
