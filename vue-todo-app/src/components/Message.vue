<script setup>
import { defineProps, defineExpose } from 'vue'; 
import { ref } from 'vue';
//   defineProps({ text: String });
   defineProps({ hidden: Boolean });
   defineEmits(['close']);

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
    <slot name="header" :someValue="42"></slot>
    <button class="delete" @click="hide"></button>
  </div>

  <div class="message-body">
   <slot :text="text">No content</slot>
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
