<script setup>
import { ref } from 'vue'
let boomNum = 10
const row = 10
const col = 10
const createLayout = () => {
  if (row !== col) {
    throw new Error('the row and col not equal')
  }
  let preList = new Array(row * col).fill(0)
  while (boomNum) {
    const index = Math.floor(Math.random() * row * col)
    if (preList[index] === 0) {
      preList[index] = -1
      boomNum--
    }
  }
  return preList
}
const list = ref(createLayout())
for (let i = 0; i < list.value.length; i++) {
  if (list.value[i] === -1) {
    // left top 
    list.value[i - row - 1] !== undefined && list.value[i - row - 1] !== -1 && list.value[i - row - 1]++
    // top
    list.value[i - row] !== undefined && list.value[i - row] !== -1 && list.value[i - row]++
    // right top
    list.value[i - row + 1] !== undefined && list.value[i - row + 1] !== -1 && list.value[i - row + 1]++
    // left
    list.value[i - 1] !== undefined && list.value[i - 1] !== -1 && list.value[i - 1]++
    // right
    list.value[i + 1] !== undefined && list.value[i + 1] !== -1 && list.value[i + 1]++
    // left bottom
    list.value[i + row - 1] !== undefined && list.value[i + row - 1] !== -1 && list.value[i + row - 1]++
    // bottom
    list.value[i + row] !== undefined && list.value[i + row] !== -1 && list.value[i + row]++
    // right bottom
    list.value[i + row + 1] !== undefined && list.value[i + row + 1] !== -1 && list.value[i + row + 1]++
  }
}
</script>

<template>
  <div class="layout">
    <div class="item" v-for="(item,index) in list" :key="index">{{item===-1?'💣':item}}</div>
  </div>
</template>

<style scoped>
* {
  user-select: none;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.layout {
  width: 500px;
  height: 500px;
  display: grid;
  grid-template-rows: repeat(v-bind(row), 1fr);
  grid-template-columns: repeat(v-bind(row), 1fr);
}

.item {
  cursor: pointer;
  /* border: 1px solid black; */
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
