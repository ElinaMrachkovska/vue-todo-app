<script setup>
import { ref, nextTick } from 'vue';

  // const { todo } = defineProps(['todo']);
  const props = defineProps(['todo']);
  const emit = defineEmits(['delete', 'update']);

  const editing = ref(false);
  const titleField = ref(null);
  const newTitle = ref(props.todo.title);

const startEditing = async () => {
  newTitle.value = props.todo.title;
  editing.value = true;

  await nextTick();

  if (titleField.value) {
    titleField.value.focus();
  }
};


const rename = () => {
  if (!editing.value) return;

  editing.value = false;

  if (newTitle.value === props.todo.title) {
    return;
  }

  if (!newTitle.value) {
    emit('delete');
    return;
  }

  emit('update', { ...props.todo, title: newTitle.value });
};

</script>

<template>
  <div 
  class="todo" 
  :class="{ completed: todo.completed }">
    <label class="todo__status-label">
      <input 
      type="checkbox" 
      class="todo__status"
      :checked="todo.completed"
    @change="emit('update', { ...todo, completed: !todo.completed })"
       />
    </label>

    <!-- show when todo is being edited -->
    <form 
    v-if="editing"
    @submit.prevent="rename">
      <input
       ref="titleField"
        v-model.trim="newTitle"
        @blur="rename"
        class="todo__title-field"
        placeholder="Empty todo will be deleted"
        @keyup.escape="editing = false"
      />
    </form>

    <template v-else>
      <span class="todo__title"
      @dblclick="startEditing">{{ todo.title }}</span>
      <button class="todo__remove" 
      @click="emit('delete')">×</button>
    </template>

    <!-- add `is-active` class when todo being processed -->
    <div class="modal overlay" :class="{ 'is-active': false }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>
</template>
