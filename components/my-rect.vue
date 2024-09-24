<script setup lang="ts">
import { customAlphabet } from 'nanoid'

defineProps<{
  x: number | string
  y: number | string
  w?: number | string
  h?: number | string
  color?: string
}>()

const nanoid = customAlphabet('abcedfghijklmn', 10)

const id = nanoid()
</script>

<template>
  <svg
    class="absolute left-0 top-0 pointer-events-none"
    :width="Number(w)+Number(x)"
    :height="Number(h)+Number(y)">
  <rect :width="w" :height="h" :x="x" :y="y" rx="0" ry="0" :fill="color" />
  </svg>
</template>
