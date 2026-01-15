<script setup>

import { ref } from 'vue';

   

const text = ref("");

const show = (newText) => {
  text.value = newText;
};

const hide = () => {
  text.value = "";
};

defineExpose({ show, hide });

</script>

<template>
<article class="message" :class="{ 'message--hidden': !text }">
  <div class="message-header">
    <slot name="header"></slot>
    <button class="delete" @click="hide"></button>
  </div>

  <div class="message-body">
   <slot :text="text"></slot>
  </div>
</article>
</template>

<style scoped lang="scss">
  .message {
    transform-origin: top center;
    transition-property: opacity, transform;
    transition-duration: 0.3s;

    &--hidden {
      transform: scaleY(0);
      opacity: 0;
      pointer-events: none;
    }
  }
</style>
