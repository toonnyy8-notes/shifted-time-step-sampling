<!--

Simple Arrow

<arrow x1="10" y1="20" x2="100" y2="200" color="green" width="3" />

<arrow v-bind="{ x1:10, y1:10, x2:200, y2:200 }"/>

-->

<script setup lang="ts">
import katex from 'katex';
import { ref, onMounted } from "vue";

let props = defineProps<{
  expr: string;
  "opt": string|null;
}>();


const root = ref<HTMLElement | null>(null);
onMounted(() => {
  if (root.value != undefined) {
    katex.render(props.expr, root.value, JSON.parse(props.opt??"{}"));
  }
});
</script>

<template>
  <span ref="root"></span>
</template>