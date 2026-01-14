
<script setup>
import { defineProps, defineEmits } from 'vue';
const props = defineProps({
  modelValue: String 
});

const emit = defineEmits(['update:modelValue']);

</script>

<template>
     <nav class="filter" data-cy="Filter">
          <a 
          href="#/" 
          class="filter__link selected" 
          data-cy="FilterLinkAll"
          :class="{ selected: modelValue === 'all' }"
          @click.prevent="emit('update:modelValue', 'all')"> All </a>

          <a 
          href="#/active" 
          class="filter__link"
           data-cy="FilterLinkActive"
           :class="{ selected: modelValue === 'active' }" 
            @click.prevent="emit('update:modelValue', 'active')"> Active </a>

          <a 
          href="#/completed" 
          class="filter__link" 
          data-cy="FilterLinkCompleted"
          :class="{ selected: modelValue === 'completed' }"
          @click.prevent="emit('update:modelValue', 'completed')"> Completed </a>
        </nav>
      </template>