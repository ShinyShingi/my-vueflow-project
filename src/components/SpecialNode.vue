<script setup lang="ts">
import { ref, onMounted, defineEmits, nextTick } from 'vue';
import { Handle } from '@vue-flow/core';
import type { NodeProps } from '@vue-flow/core';

const props = defineProps<NodeProps>();
const emit = defineEmits(['bookClick', 'updateSize']);

const nodeRef = ref<HTMLElement | null>(null);

const updateSize = () => {
  if (nodeRef.value) {
    const size = {
      id: props.id,
      width: nodeRef.value.offsetWidth,
      height: nodeRef.value.offsetHeight,
    };
    console.log(`Node ID: ${props.id}, Width: ${size.width}, Height: ${size.height}`);
    emit('updateSize', size);
  }
};

onMounted(async () => {
  await nextTick(); // Ensure the DOM is rendered
  updateSize();
});
</script>

<template>
  <div ref="nodeRef" class="vue-flow__node-default">
    <div>{{ props.data.label }}</div>
    <ol v-if="props.data.books">
      <li v-for="book in props.data.books" :key="book.id" @click="emit('bookClick', book)">
        {{ book.title }}
      </li>
    </ol>
    <Handle type="source" position="bottom" />
  </div>
</template>
