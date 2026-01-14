<script setup>
  import * as todoApi from './api/todo';
 import { ref, computed, onMounted} from 'vue';
 import StatusFilter from "./components/StatusFilter.vue";
 import { defineProps, defineEmits } from 'vue';
 import TodoItem from "./components/TodoItem.vue";
 import { TransitionGroup } from 'vue';
 import  Message from "./components/Message.vue";

const USER_ID = 1; 
const title = ref("");
const props = defineProps({
  status: String,
});
const { status } = props;
const currentStatus = ref("all");

  const errorMessage = ref('');
  const messageComponent = ref(null);
  const values = ref({ title: "" });
  const errors = ref({ title: "" });
  const emit = defineEmits(['change']);
  

    const todos = ref([]);
    

onMounted(async () => {
  try {
    todos.value = await todoApi.getTodos();
  } catch (error) {
    messageComponent.value.show('Unable to load todos');
  }
});


let initialTodos = [];

try {
  initialTodos = JSON.parse(localStorage.getItem('todos'));
} catch (error) {}

if (!Array.isArray(initialTodos)) {
  initialTodos = [];
}

const addTodo = async () => {
  if (!title.value) {
    messageComponent.value.show('Title should not be empty');
    return;
  }

  try {
    const newTodo = await todoApi.createTodo(title.value);

    todos.value.push(newTodo);
    title.value = '';
  } catch (error) {
    messageComponent.value.show('Unable to add a todo');
  }
};


const deleteTodo = async todoId => {
  try {
    await todoApi.deleteTodo(todoId);
    todos.value = todos.value.filter(todo => todoId !== todo.id);
  } catch (error) {
    messageComponent.value.show('Unable to delete a todo');
  }
};


const updateTodo = async ({ id, title, completed }) => {
  try {
    const updatedTodo = await todoApi.updateTodo({ id, title, completed });
    const currentTodo = todos.value.find(todo => todo.id === id);

    Object.assign(currentTodo, updatedTodo);
  } catch (error) {
    messageComponent.value.show('Unable to update a todo');
  }
};

  const activeTodos = computed(() =>
  todos.value.filter((todo) => !todo.completed));


const visibleTodos = computed(() => {
  // Тепер використовуємо локальний currentStatus.value
  if (currentStatus.value === "active") {
    return activeTodos.value;
  }
  if (currentStatus.value === "completed") {
    return todos.value.filter(todo => todo.completed);
  }
  return todos.value;
});


</script>
<!-- eslint-disable jsx-a11y/label-has-associated-control -->
<!-- eslint-disable jsx-a11y/control-has-associated-label -->
<template>
  <!-- Якщо немає USER_ID -->
  <div v-if="!USER_ID">
    <p>Please set your USER_ID</p>
  </div>

  <div v-else class="todoapp">
    <h1 class="todoapp__title">todos {{ todos.length }}</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <!-- this button should have `active` class only if all todos are completed -->
        <button
          type="button"
          v-if="todos.length > 0"
          class="todoapp__toggle-all"
          data-cy="ToggleAllButton"
          :class="{ active: activeTodos.length === 0}"
        ></button>

        <!-- Add a todo on form submit -->
        <form @submit.prevent="addTodo">
          <input
            data-cy="NewTodoField"
            v-model="title"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            @input="errorMessage = ''"
          />
        </form>
      </header>

      <section class="todoapp__main" data-cy="TodoList">
        <!-- This is a completed todo -->
         <TransitionGroup
  tag="section"
  name="todolist"
  class="todoapp__main"
  v-if="todos.length > 0"
>
        <TodoItem
  v-for="todo of visibleTodos"
  :key="todo.id"
  :todo="todo"
  @delete="deleteTodo(todo.id)"
  @update="updateTodo($event)"
/>
         </TransitionGroup>
      </section>
      <!-- Hide the footer if there are no todos -->
      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count" 
        data-cy="TodosCounter"
        > {{ activeTodos.length }} items left </span>

        <!-- Active link should have the 'selected' class -->
   <StatusFilter v-model="currentStatus"/>

        <!-- this button should be disabled if there are no completed todos -->
        <button 
        type="button"
        class="todoapp__clear-completed"
         data-cy="ClearCompletedButton"
         :disabled="todos.length === activeTodos.length"
         @click="todos = activeTodos">
          Clear completed
        </button>
      </footer>
    </div>

    <!-- DON'T use conditional rendering to hide the notification -->
    <!-- Add the 'hidden' class to hide the message smoothly -->
    <div data-cy="ErrorNotification"
    v-if="errorMessage" 
    class="notification is-danger is-light has-text-weight-normal">
      <button 
      data-cy="HideErrorButton" 
      type="button" 
      class="delete"
      @click="errorMessage = ''">
<Message class="is-danger" ref="messageComponent">
<template #header="{ someValue }">
  <p>Server Error {{ someValue }}</p>
</template>


  <template #default>
    <p>{{ errorMessage }}</p>
  </template>
</Message>


    </button>
      <!-- show only one message at a time -->
     

    </div>
  </div>
 
</template>
<style scoped>
  .todolist-enter-active,
  .todolist-leave-active {
    max-height: 60px;
    transition: all 0.5s ease;
  }
  .todolist-enter-from,
  .todolist-leave-to {
    opacity: 0;
    max-height: 0;
    transform: scaleY(0);
  }
</style>
