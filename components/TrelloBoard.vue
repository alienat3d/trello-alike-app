<script setup lang="ts">
// 2.1.0 Также необходимо добавить тип для колонки, который мы заранее подготовили.
import type { Column } from "~/types";
import { nanoid } from "nanoid";
// 4.0 Добавим библиотеку «Vue Draggable», чтобы иметь доступ к разным фичам и анимации, для создания Drag & Drop функционала. ↓
import draggable from "vuedraggable";
import TrelloBoardTask from "./TrelloBoardTask.vue";

// * 2.0 Теперь приступим к созданию какого-то рыбного контента на странице, чтобы стилизовать приложение. И раз уж мы во Vue, то сделаем это при помощи его мощностей. Создадим реактивный массив для колонок.
// ? Нам не приходится импортировать "ref", т.к. мы работаем в Nuxt и это один из его приятных бонусов.
// 2.1.1 Далее мы используем этот тип дженериком, чтобы указать, какого типа у нас будет этот массив и его структура.
// 2.1.2 Далее внутри в "{}" мы опишем, что именно будет содержать в себе массива, это: id, сгенерированный автоматически при помощи библиотеки "nanoid", заголовок и массив с задачами, который также содержит уникальный id, заголовок и время создания задачи.
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
</script>
<!-- 2.1.3 После того, как структура определена можно заняться генерацией вёрстки с помощью цикла в директиве "v-for". Мы заполним содержимое тегов из свойств объектов описанных выше в массиве данных. -->
<template>
  <div>
    <!-- 2.2 Также начнём стилизацию с помощью функциональных классов TailwindCSS. И добавим класс "column", но уже не для стилизации, а для индикации элемента колонки с задачами. -->
    <!-- Go to [components\TrelloBoardTask.vue] -->
    <!-- 4.1 Теперь используем комп. "draggable" внутри внешней обёртки колонки. Итак, этот компонент будет получать массив в виде "v-model", чьё содержимое мы хотим отобразить (в нашем случае это "columns"). Такой подход делает отображение каждой колонки с помощью «Vue Draggable», а также, когда мы перемещаем колонку, то она будет обновлять массив при помощи «Vue Draggable», а именно поменяет порядок перетаскиваемого элемента в массиве. -->
    <!-- 4.2 Нам также понадобится проп "group" и дадим статичное значение "columns". Этот проп даёт нам возможность перемещать колонку из одного списка в другой. (В общем, в нашем случае это не слишком обязательно, т.к. фактически пока у нас лишь один список. Но позже, когда у нас появятся задачи в других списках тоже, то польза от этого пропа будет видна более явно.) -->
    <!-- 4.3 Ещё один проп, который может пригодиться — "item-key". С помощью него мы информируем Vue Draggable как мы хотим идентифицировать каждый из элементов внутри списков. Значением будет "id". (Вероятнее всего "под капотом" Vue Draggable просто присвоит элементам атрибут "key" с уникальным id для цикла в директиве "v-for", а может и что-то ещё.) -->
    <!-- 4.4 Также этот комп. "draggable" будет служить обёрткой для всех наших колонок. Поэтому заберём классы у внешнего div'а и повесим на комп. "draggable". -->
    <!-- 4.5 Внутри "draggable" мы определим slot для каждого отдельного элемента, который будет содержаться в нём. И на этом этапе мы теперь уже работаем с каждой из колонок, а значит нам не нужна здесь директива "v-for". Перетащим вёрстку колонки внутрь слота. -->
    <draggable
      v-model="columns"
      group="columns"
      item-key="id"
      class="flex items-start gap-4 overflow-x-auto pb-10"
    >
      <!-- 4.6 Теперь у нас потерялся доступ к данным из "column", чтобы вновь его обрести извлечём "column" из доступной переменной "element" в "#item". Также здесь укажем тип для element - "Column". -->
      <template #item="{ element: column }: { element: Column }">
        <div class="column min-w-[250px] rounded bg-gray-200 p-5">
          <header class="mb-4 font-bold">{{ column.title }}</header>
          <TrelloBoardTask
            :task="task"
            v-for="task in column.tasks"
            :key="task.id"
          />
          <footer><button class="text-gray-500">+ Add a Card</button></footer>
        </div>
      </template>
    </draggable>
    <!-- <div
      v-for="column in columns"
      :key="column.id"
      class="column min-w-[250px] rounded bg-gray-200 p-5"
    >
      <header class="mb-4 font-bold">{{ column.title }}</header> -->
    <!-- 3.3 Теперь мы добавим комп. TrelloBoardTask сюда, чтобы отображать задачи. Он будет принимать "task" и нам также надо добавить к нему цикл, чтобы распечатать их всех на страницу. -->
    <!-- Go to [components\TrelloBoardTask.vue] -->
    <!-- <TrelloBoardTask
        :task="task"
        v-for="task in column.tasks"
        :key="task.id"
      /> -->
    <!-- <p v-for="task in column.tasks" :key="task.id">{{ task.title }}</p> -->
    <!-- 3.5 Нам также необходимо добавить кнопку добавления новых задач. ↑ -->
    <!--   <footer><button class="text-gray-500">+ Add a Card</button></footer>
    </div> -->
  </div>
</template>
