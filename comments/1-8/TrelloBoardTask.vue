<!-- * 3.0 Здесь мы создадим отдельный компонент под каждую из задач на доске. Где мы импортируем тип "Task", а также создадим пропы с помощью метода "defineProps". -->
<script setup lang="ts">
import type { Task } from "~/types";

// 3.1.0 В JavaScript мы бы создали пропы таким образом:
/* defineProps({
  task: Object,
}); */
// 3.1.1 Однако здесь в TypeScript мы хотим сказать, что структура Task должна быть взята из интерфейса Task. Но просто так указать его внутри описания пропов мы не можем, т.к.к "Task" это тип TypeScript. Вместо этого нам нужно записать чуть иначе, используя дженерик в defineProps. Этот дженерик получит объект, где мы опишем пропы и здесь уже можем использовать интерфейс "Task".
defineProps<{
  task: Task;
}>();
</script>

<!-- 3.2 Ок, переходим к созданию вёрстки компонента, где атрибутом "title" забиндим дату создания задачи. А текстовым контентом будет содержимое "title", т.е. название задачи. -->
<!-- Go to [components\TrelloBoard.vue] -->
<!-- 3.4 Дадим элементу класс "task", опять же не для стилизации, но для идентификации элемента, что это корень компонента задач, но также и стилизуем его немного. -->
<!-- Go to [components\TrelloBoard.vue] -->
<template>
  <div
    :title="task.createdAt.toLocaleDateString()"
    class="task mb-2 max-w-[250px] rounded bg-white p-2 shadow-sm"
  >
    <span>{{ task.title }}</span>
  </div>
</template>
