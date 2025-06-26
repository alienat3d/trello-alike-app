<script setup lang="ts">
import type { Task } from "~/types";
import { nanoid } from "nanoid";

// 9.1 Здесь мы создаём кастомное событие "add" для создания задачи, а также с ним будет передаваться и объект "Task" как payload.
const emit = defineEmits<{
  (e: "add", payload: Task): void;
}>();

// 9.2.0 Мы также создадим две ref-переменные, где будем отслеживать сфокусирована ли область создания новой задачи или нет, а также название задачи, которое будет передаваться директивой v-model из textarea в реф-переменную "title".
const focused = ref(false);
const title = ref("");

// 9.4.0 Функция "createTask" сперва проверяет есть ли вообще что-то в "title", затем мы останавливаем стандартное поведение для события, где обычно для клавиши "Tab" это сменить фокус на следующий интерактивный элемент в DOM.
// 9.4.1 Затем создаётся кастомное событие "add" и в нём также объект с title, createdAt и id полями, образующими новую задачу. И в конце прописывается этому объекту тип Task.
// Go to [components\TrelloBoard.vue]
function createTask(e: Event) {
  if (title.value.trim()) {
    e.preventDefault();
    emit("add", {
      title: title.value.trim(),
      createdAt: new Date(),
      id: nanoid(),
    } as Task);
  }

  title.value = "";
}
</script>

<!-- * 9.0 Ок, теперь займёмся созданием новой фичи — добавление новых задач в список. Для этого мы создали этот новый компонент, который будет этим заниматься. -->

<template>
  <div>
    <!-- 9.2.1 Делаем мы это с помощью двух слушателей событий "focus" & "blur" для фокусировки на элементе и расфокусировки соответственно. Исходя из того, что будет находиться в реф-переменной "focused", будет меняться placeholder textarea, а также изменяться высота этого элемента. -->
    <!-- 9.3 Также мы добавим слушатели событий для нажатия клавиш "Tab" & "Enter" и они будут запускать функцию "createTask", которая собственно и будет создавать новую задачу. ↑ -->
    <textarea
      v-model="title"
      @focus="focused = true"
      @blur="focused = false"
      @keydown.tab="createTask"
      @keyup.enter="createTask"
      class="w-full cursor-pointer resize-none overflow-y-hidden rounded border-none p-2 focus:bg-white focus:shadow"
      :class="{ 'h-7': !focused, 'h-20': focused }"
      style="outline: none !important"
      :placeholder="!focused ? '+ Add a card' : 'Enter a title for this card'"
    ></textarea>
  </div>
</template>
