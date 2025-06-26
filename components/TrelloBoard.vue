<script setup lang="ts">
import type { Column, Task } from "~/types";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";
import TrelloBoardTask from "./TrelloBoardTask.vue";
import NewTask from "./NewTask.vue";

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

const isAltPressed = useKeyModifier("Alt");

// 11.3.0 А также на нужно создать функцию "createColumn", которая будет создавать новый объект "column" и пушать его в массив "columns". Объект будет содержать id, случайно генерируем с помощью либы «nanoid», заголовок и пустой массив с задачами. И не забудем присвоить созданный нами вначале тип "Column", ведь мы работаем с TS.
// 11.3.1 Затем мы будем пушать новый объект в массив с объектами "columns".
// 11.4 Здорово, но можно улучшить, чтобы при создание новой колонки происходила фокусировка на заголовке новой колонки. Чтобы это сделать найдём заголовок посл. колонки по селектору ".column:last-of-type  .title-input" и сфокусируемся на нём методом "focus". Но также добавим "?-оператор", что будет означать "если существует", а также укажем тип HTMLInputElement.
// ? 11.5 Ещё мы добавим проверку, что Vue на самом деле обновил DOM перед фокусировкой на заголовке. Сделать этом можно базовым Vue-методом nextTick, в который мы обернём кусочек кода для нахождения заголовка и фокусировки на нём. В идеале надо всегда так делать перед тем, как опрашивать добавляемые вручную элементы DOM. ↓
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
  <!-- 11.2 Добавим пару стилевых классов, чтобы расположить кнопку и колонки рядом по верхней границы контейнера. -->
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
            <!-- * 11.0 Теперь займёмся самими колонками. Во-первых, нужно сделать их заголовки редактируемыми, чтобы по двойному клику на заголовке можно было бы его изменить. Для этого добавим инпут-элемент. Давайте рассмотрим его по порядку: у него есть v-model, который привязывает его к свойству "title" объекта "column" и это обеспечивает реактивное редактирование заголовка. Также сохранение данных происходит по нажатию "Enter" с помощью слушателя события "keyup" и метода "blur". Также с помощью Tailwind-классов "bg-transparent" и "focus:bg-white" мы создаём эффект, что по двойному клику заголовок становится строкой ввода. -->
            <!-- 11.6 Также нам нужно создать функцию удаления наших колонок. И здесь мы могли бы, как и в случае с задачами, создать где-то кнопку "х", но вместо этого поработаем над чуть иным функционалом. А именно установим слушатель события "keydown" на кнопку "Backspace". Затем мы запишем значением, что если у колонки нет заголовка, а пользователь нажал "Backspace", то колонка удалится. Сделаем это с помощью метода "filter", который вернёт все колонки, кроме конкретной, у которой id совпадает с той, по которой кликнули и присвоит результат массиву с колонками. -->
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
    <!-- 11.1 Также, в самом конце, после всех колонок, нам нужно сделать кнопку, добавляющую новые колонки в список. ↑ -->
    <button
      @click="createColumn"
      class="min-w-[250px] cursor-pointer rounded bg-gray-200 p-4 text-left whitespace-nowrap opacity-50"
    >
      + Add another column
    </button>
  </div>
</template>
