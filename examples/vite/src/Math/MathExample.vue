<script setup lang="ts">
import { VueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'
import { initialEdges, initialNodes } from './initialElements'
import ValueNode from './ValueNode.vue'
import OperatorNode from './OperatorNode.vue'
import ResultNode from './ResultNode.vue'
import './styles.css'

const nodes = ref(initialNodes)

const edges = ref(initialEdges)
</script>

<template>
  <VueFlow class="math-flow" :nodes="nodes" :edges="edges" fit-view-on-init>
    <template #node-value="props">
      <ValueNode :id="props.id" :data="props.data" />
    </template>

    <template #node-operator="props">
      <OperatorNode :id="props.id" :data="props.data" />
    </template>

    <template #node-result="props">
      <ResultNode :id="props.id" />
    </template>

    <Background />
  </VueFlow>
</template>
