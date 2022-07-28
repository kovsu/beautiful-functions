<script setup lang="ts">
import { computed } from "@vue/reactivity";
import { onMounted, ref } from "vue";

const expression = ref("Math.sin(x * Math.PI / 180)");
const canvas = ref<HTMLCanvasElement>();
const ctx = computed(() => canvas.value!.getContext("2d")!);
// canvas 的 width, 每个点为 1, 所以一个 canvas 上一次画 500 个点
const range = 500;
let step = 0;

function Fn(x: number) {
  // console.log(eval(`100 * ${expression.value} + 100`));
  return eval(`100 * ${expression.value} + 100`);
}

function drawMovePoint(x: number, y: number) {
  ctx.value.beginPath();
  ctx.value.arc(x, y, 5, 0, 2 * Math.PI);
  ctx.value.fillStyle = "#f11";
  ctx.value.fill();
  ctx.value.strokeStyle = "#000";
  ctx.value.stroke();
}

function drawSin(start: number, end: number) {
  ctx.value.beginPath();
  for (let i = start; i < end; i++) {
    ctx.value.lineTo(i - step, Fn(i));
    ctx.value.stroke();
  }
}

onMounted(() => {
  ctx.value.beginPath();
  ctx.value.moveTo(0, 100);
  drawSin(step, step + range);
  drawMovePoint(100, Fn(100));
});

function startFrame() {
  requestAnimationFrame(() => {
    ctx.value.clearRect(0, 0, 500, 200);
    step += 1;
    drawSin(step, step + range);
    drawMovePoint(100, Fn(100 + step));
    startFrame();
  });
}

startFrame();
</script>

<template>
  <div class="container">
    <h1>{{ expression }}</h1>
    <canvas
      ref="canvas"
      id="myCanvas"
      width="500"
      height="200"
      style="border: 1px solid lightgrey"
    ></canvas>
    <div class="expression__input">
      <p class="title">(x) =></p>
      <input type="text" v-model="expression" @blur="startFrame" />
    </div>
  </div>
</template>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

#myCanvas {
  padding: 10px 20px;
  display: block;
  margin: 20px auto;
}

.expression__input {
  width: 30%;
}

.title {
  margin: 0;
  font-size: 24px;
  font-weight: 700;
  color: rgb(173, 173, 173);
  line-height: 1.4;
}

.expression__input input {
  font-size: 16px;
  padding: 5px 10px;
  border: none;
  outline: none;
  border-bottom: 1px solid #000;
}
</style>
