<script setup lang="ts">
import { ref } from 'vue'
import type { Node, Edge } from '@vue-flow/core'
import { VueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'
import SpecialNode from './components/SpecialNode.vue'
import SpecialEdge from './components/SpecialEdge.vue'
import TextNode from './components/TextNode.vue';



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
  const x = centerX + previousNodesWidth;

  console.log(`Placing New Node: ${book.title}, X: ${x}, Y: ${y}`);

  const newNode = {
    id: book.id,
    position: { x, y },
    data: { label: `${book.title} - ${book.author} (${book.year})`, author: book.author, year: book.year },
  };

  const newEdge = {
    id: `e1-${book.id}`,
    source: mainNodeId,
    target: book.id,
  };


  nodes.value.push(newNode);
  edges.value.push(newEdge);

  console.log("Updated Nodes List:", JSON.parse(JSON.stringify(nodes.value)));
  console.log("Updated Edges List:", JSON.parse(JSON.stringify(edges.value)));



  nodeCount.value++; // Increment for the next node
};

// Handle when an edge is created
const onEdgeConnect = (connection) => {
  const newEdge = {
    id: `e-${connection.source}-${connection.target}`,
    source: connection.source,
    target: connection.target,
  };

  edges.value.push(newEdge);
  console.log(`Edge Created: ${newEdge.id}`);
};

// Handle when an edge is updated (moved)
const onEdgeUpdate = ({ edge, connection }) => {
  console.log(`Edge Updated from ${edge.source} → ${edge.target} to ${connection.source} → ${connection.target}`);

  edges.value = edges.value.map(e =>
      e.id === edge.id ? { ...e, source: connection.source, target: connection.target } : e
  );
};

// Handle when an edge is clicked (manual deletion)
const onNodeClick = (event) => {
  console.log("Raw Node Click Event:", event);

  if (!event.node || !event.node.id) {
    console.error("Node ID is missing! The event did not provide the correct node data.");
    return;
  }

  console.log(`Node Clicked: ${event.node.id}`);

  // Find the full node object from nodes.value
  const fullNode = nodes.value.find(n => n.id === event.node.id);

  if (!fullNode) {
    console.error("Node not found in the list.");
    return;
  }

  console.log("Full Node Data:", JSON.parse(JSON.stringify(fullNode)));
};




// Handle when a node is clicked
const onEdgeClick = (event) => {
  console.log("Raw Edge Click Event:", event);

  if (!event.edge || !event.edge.id) {
    console.error("Edge ID is missing! The event did not provide the correct edge data.");
    return;
  }

  console.log(`Edge Clicked: ${event.edge.id}`);

  // Find the full edge object from edges.value
  const fullEdge = edges.value.find(e => e.id === event.edge.id);

  if (!fullEdge) {
    console.error("Edge not found in the list.");
    return;
  }

  console.log("Full Edge Data:", JSON.parse(JSON.stringify(fullEdge)));

  //  Remove the clicked edge
  edges.value = edges.value.filter(e => e.id !== fullEdge.id);
  console.log(`Edge Deleted Successfully: ${fullEdge.id}`);

  //  Remove disconnected nodes (if they no longer have edges)
  const connectedNodes = new Set(edges.value.flatMap(e => [e.source, e.target]));
  nodes.value = nodes.value.filter(node => node.id === mainNodeId || connectedNodes.has(node.id));

  console.log("Updated Nodes List:", nodes.value);
};

const addTextNode = () => {
  const x = 500 + nodeCount.value * 160;
  const y = 500;

  const newNode = {
    id: `t-${nodeCount.value}`,
    type: 'text',
    position: { x, y },
    data: { text: "Enter text..." },
  };

  nodes.value.push(newNode);
  nodeCount.value++;
};

</script>

<template>
  <div class="container">
    <button @click="addTextNode">➕ Add Text Node</button>
    <VueFlow
        v-model:nodes="nodes"
        v-model:edges="edges"
        :defaultEdgeOptions="{ deletable: true, updatable: true, selectable: true }"
        @edgeClick="onEdgeClick"
        @connect="onEdgeConnect"
        @edgeUpdate="onEdgeUpdate"
        @nodeClick="onNodeClick"
    >
      <Background />

      <template #node-special="specialNodeProps">
        <SpecialNode
            v-bind="specialNodeProps"
            @bookClick="addBookNode"
            @updateSize="updateNodeSize"
        />
      </template>
      <template #node-text="textNodeProps">
        <TextNode
            v-bind="textNodeProps"
            @updateSize="updateNodeSize"
            @deleteNode="deleteNode"
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
button {
  position: absolute;
  z-index: 10000;
  top: 20px;
  margin-bottom: 10px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
}

button:hover {
  background-color: #0056b3;
}
</style>
