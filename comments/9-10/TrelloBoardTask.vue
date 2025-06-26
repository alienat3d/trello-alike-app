<script setup lang="ts">
import type { Task, ID } from "~/types";

const props = defineProps<{
  task: Task;
}>();

// 10.4.1 Естественно нам также понадобится "defineEmits" для кастомного события. В payload мы будем отправлять и т.к. у нас есть специальный тип для этого, то отправим именно его в дженерик как тип.
// Go to [components\TrelloBoard.vue]
const emit = defineEmits<{
  (e: "delete", payload: ID): void;
}>();

// * 10.0 Займёмся созданием функции удаления задач из списка. Мы могли бы сделать маленькую кнопку "×" для удаления задач. Но здесь мы пойдём несколько иным путём: мы сделаем задачи доступными для фокусировки и по нажатию кнопки "Backspace" задача в фокусе будет удаляться. Для этого мы сперва создадим реактивную ref-переменную, по образу, как мы делали в [components\NewTask.vue].
const focused = ref(false);

// 10.3 Теперь мы создадим слушатель также для клавиши "Backspace". Для этого используем composable-функцию "onKeyStroke", которая получает первым аргументом клавишу, которую мы хотим слушать. А вторым аргументом идёт коллбэк-функция, которая запускается, после того как эту клавишу нажмут.
// 10.4.0 Внутри будем проверять, что focused в положении true и тогда создадим кастомное событие "delete".
onKeyStroke("Backspace", (e) => {
  if (focused.value) emit("delete", props.task.id);
});
</script>

<template>
  <!-- 10.1 Затем мы будем слушать каждый конкретный элемент задачи на предмет фокусировки на нём и тогда присваивать переменной "focused" значение "true", а при снятии фокуса снова на "false". -->
  <!-- 10.2 Ещё нам нужно добавить атрибут "tabindex", чтобы фокусировка работала, т.к. div не тот элемент по умолчанию, на котором браузеры фокусируются. -->
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

<!-- * 8.0 В оригинальном «Trello» при перетаскивании элементы задач наклоняются в одну сторону, мы можем сделать тоже самое. «SoratableJS» даёт нам три класса для разных состояний: "sortable-chosen" (когда мы только начали перетаскивать элемент кликнув на нём и удерживая кнопку мыши.), "sortable-drag" (когда мы начали движение удерживая элемент) и "sortable-ghost" (применяется к дублирующему полупрозрачному отображению элемента, показывающее где он упадёт, если отпустить кнопку мыши). В нашем примере "sortable-chosen" не пригодится. -->
<!-- 8.1 При перетаскивании элемента мы хотим слегка наклонять вбок элемент задачи наподобие как в оригинальном «Trello». ↑ -->
<!-- 8.3 Теперь займёмся стилизацией "призрака", для достижения эффекта, как у оригинального «Trello» лучше всего подойдёт псевдоэлемент. -->
<!-- Go to [components\NewTask.vue] -->

<!-- 10.6 Но пока у нас нет никакой индикации, что какая-то задача в фокусе. Исправим это. -->
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

.task:focus,
.task:focus-visible {
  @apply !outline-gray-400;
  outline: gray auto 1px;
}
</style>
