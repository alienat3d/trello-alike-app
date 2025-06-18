<script setup lang="ts">
import type { Column } from '~/types';
// 2.1.0 Также необходимо добавить тип для колонки, который мы заранее подготовили.
import { nanoid } from 'nanoid';

// * 2.0 Теперь приступим к созданию какого-то рыбного контента на странице, чтобы стилизовать приложение. И раз уж мы во Vue, то сделаем это при помощи его мощностей. Создадим реактивный массив для колонок.
// ? Нам не приходится импортировать "ref", т.к. мы работаем в Nuxt и это один из его приятных бонусов.
// 2.1.1 Далее мы используем этот тип дженериком, чтобы указать, какого типа у нас будет этот массив и его структура.
// 2.1.2 Далее внутри в "{}" мы опишем, что именно будет содержать в себе массива, это: id, сгенерированный автоматически при помощи библиотеки "nanoid", заголовок и массив с задачами, который также содержит уникальный id, заголовок и время создания задачи.
const columns = ref<Column[]>([
  {
    title: 'Backlog',
    id: nanoid(),
    tasks: [
      {
        title: 'Create a landing page',
        createdAt: new Date(),
        id: nanoid(),
      },
      {
        title: 'Develop a new feature',
        createdAt: new Date(),
        id: nanoid(),
      },
      {
        title: 'Fix the page navigation bug',
        createdAt: new Date(),
        id: nanoid(),
      },
    ],
  },
  { title: 'Selected for Dev', id: nanoid(), tasks: [] },
  { title: 'In Progress', id: nanoid(), tasks: [] },
  { title: 'QA', id: nanoid(), tasks: [] },
  { title: 'Complete', id: nanoid(), tasks: [] },
]);
</script>

<!-- 2.1.3 После того, как структура определена можно заняться генерацией вёрстки с помощью цикла в директиве "v-for". Мы заполним содержимое тегов из свойств объектов описанных выше в массиве данных. -->
<template>
  <div>
    <div v-for="column in columns" :key="column.id">
      <header>{{ column.title }}</header>
      <p v-for="task in column.tasks" :key="task.id">{{ task.title }}</p>
    </div>
  </div>
</template>
