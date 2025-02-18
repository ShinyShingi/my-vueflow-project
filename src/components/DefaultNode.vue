<script setup lang="ts">
import { ref, onMounted, defineEmits, nextTick } from "vue";
import { Handle } from "@vue-flow/core";
import type { NodeProps } from "@vue-flow/core";

const props = defineProps<NodeProps>();
const emit = defineEmits(["updateSize"]);

const nodeRef = ref<HTMLElement | null>(null);

const updateSize = () => {
  if (nodeRef.value) {
    const size = {
      id: props.id,
      width: nodeRef.value.offsetWidth,
      height: nodeRef.value.offsetHeight,
    };
    console.log(`Main Node ID: ${props.id}, Width: ${size.width}, Height: ${size.height}`);
    emit("updateSize", size);
  }
};

onMounted(async () => {
  await nextTick();
  updateSize();
});
</script>

<template>
  <div ref="nodeRef" class="vue-flow__node-default main-node">
    <div class="node-header">
      <strong>{{ props.data.label }}</strong>
    </div>

    <!-- Show book list -->
    <ol v-if="props.data.books && props.data.books.length">
      <li v-for="book in props.data.books" :key="book.id">
        {{ book.title }}
      </li>
    </ol>

    <Handle type="source" position="bottom" />
  </div>
</template>

<style>
.main-node {
  background-color: lightblue;
  border: 2px solid darkblue;
  padding: 10px;
  border-radius: 8px;
  min-width: 200px;
}

.node-header {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 8px;
}
</style>
