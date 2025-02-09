<script setup lang="ts">
import { ref } from 'vue'
import type { Node, Edge } from '@vue-flow/core'
import { VueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'
import SpecialNode from './components/SpecialNode.vue'
import SpecialEdge from './components/SpecialEdge.vue'


const mainNodeId = '1';

const nodes = ref<Node[]>([
  {
    id: mainNodeId,
    type: 'special',
    position: { x: 500, y: 300 }, // Set initial position to the center
    draggable: false, // Prevent dragging the main node
    data: {
      label: 'Books List',
      books: [
        { id: 'b1', title: 'The Hobbit', author: 'J.R.R. Tolkien', year: 1937 },
        { id: 'b2', title: 'Mistborn', author: 'Brandon Sanderson', year: 2006 },
        { id: 'b3', title: 'The Name of the Wind', author: 'Patrick Rothfuss', year: 2007 },
        { id: 'b4', title: 'Dune', author: 'Frank Herbert', year: 1965 },
        { id: 'b5', title: '1984', author: 'George Orwell', year: 1949 },
        { id: 'b6', title: 'Brave New World', author: 'Aldous Huxley', year: 1932 },
        { id: 'b7', title: 'The Catcher in the Rye', author: 'J.D. Salinger', year: 1951 },
        { id: 'b8', title: 'To Kill a Mockingbird', author: 'Harper Lee', year: 1960 },
        { id: 'b9', title: 'Fahrenheit 451', author: 'Ray Bradbury', year: 1953 },
        { id: 'b10', title: 'The Great Gatsby', author: 'F. Scott Fitzgerald', year: 1925 }
      ],
    },
  }
]);


const edges = ref<Edge[]>([]); // No edges at the start

const nodeSizes = ref<Record<string, { width: number; height: number }>>({});

// Function to store node size when received
const updateNodeSize = ({ id, width, height }) => {
  nodeSizes.value[id] = { width, height };
  console.log(`Updated Size for Node ID: ${id} -> Width: ${width}, Height: ${height}`);
};

const nodeCount = ref(0); // Track the number of book nodes added

const addBookNode = (book: { id: string; title: string; author: string; year: number }) => {
  const mainNode = nodeSizes.value[mainNodeId] || { width: 172, height: 298 }; // Main node size
  const spacing = 10; // Space between nodes
  const bookNodeWidth = nodeSizes.value[book.id]?.width || 150; // Dynamically get width (fallback to 150)

  // Calculate total width of all existing nodes for proper horizontal centering
  const previousNodesWidth = nodeCount.value * (bookNodeWidth + spacing);
  const totalWidth = previousNodesWidth + bookNodeWidth;

  const centerX = 500; // Center X of the main node
  const centerY = 300; // Center Y of the main node

  // Y position: Directly below the main node
  const y = centerY + mainNode.height + 20;

  // X position: Place nodes in a row, centered relative to the main node
  const x = centerX  + previousNodesWidth;

  console.log(`Placing New Node: ${book.title}, X: ${x}, Y: ${y}`);

  const newNode = {
    id: book.id,
    position: { x, y },
    data: { label: `${book.title} (${book.year})`, author: book.author, year: book.year },
  };

  const newEdge = {
    id: `e1-${book.id}`,
    source: mainNodeId,
    target: book.id,
  };

  nodes.value.push(newNode);
  edges.value.push(newEdge);

  nodeCount.value++; // Increment for the next node
};




</script>

<template>
  <div class="container">
    <VueFlow :nodes="nodes" :edges="edges">
      <Background />

      <template #node-special="specialNodeProps">
        <SpecialNode v-bind="specialNodeProps"
                     @bookClick="addBookNode"
                     @updateSize="updateNodeSize"
        />
      </template>
    </VueFlow>
  </div>
</template>



<style>
@import '@vue-flow/core/dist/style.css';
@import '@vue-flow/core/dist/theme-default.css';
@import "style.css";

.container {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

</style>
