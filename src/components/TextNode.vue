<script setup lang="ts">
import { ref, defineEmits, onMounted, nextTick } from 'vue';
import { Handle } from '@vue-flow/core';
import type { NodeProps } from '@vue-flow/core';

const props = defineProps<NodeProps>();
const emit = defineEmits(['updateSize', 'deleteNode']);
const nodeRef = ref<HTMLElement | null>(null);
const textValue = ref(props.data.text || "Type here...");

const updateSize = () => {
  if (nodeRef.value) {
    const size = {
      id: props.id,
      width: nodeRef.value.offsetWidth,
      height: nodeRef.value.offsetHeight,
    };
    emit('updateSize', size);
  }
};

onMounted(async () => {
  await nextTick();
  updateSize();
});
</script>

<template>
  <div ref="nodeRef" class="vue-flow__node-default text-node">
    <textarea v-model="textValue"></textarea>
    <Handle type="source" position="bottom" />
    <Handle type="target" position="top" />
  </div>
</template>

<style>
.text-node {
  background-color: #f8f9fa;
  border: 2px solid #ccc;
  padding: 10px;
  border-radius: 8px;
  width: 150px;
}

textarea {
  width: 100%;
  height: 60px;
  resize: none;
  border: 1px solid #ddd;
  padding: 4px;
  border-radius: 4px;
}


</style>
