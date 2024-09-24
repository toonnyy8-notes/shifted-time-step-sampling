<script setup lang="ts">
import { ref, useTemplateRef, onMounted } from 'vue'

const props = defineProps({
  count: {
    default: 0,
  },
})

const canvasRef = useTemplateRef('canvas')

onMounted(() => {
    let ctx = canvasRef.value?.getContext("2d");
    if (ctx) {

      ctx.beginPath();
      ctx.moveTo(0, 50);
      Array(1000).fill(0).map((_, i)=>{
        ctx.lineTo(i, 100-Math.E**(-((i/10-75)**2))*100)
      })
      ctx.stroke();
      // ctx.fillRect(25, 25, 100, 100);
      // ctx.clearRect(45, 45, 60, 60);
      // ctx.strokeRect(50, 50, 50, 50);
    }
})

const counter = ref(props.count)
</script>

<template>
  <canvas ref="canvas" width="1000" height="150"></canvas>
</template>
