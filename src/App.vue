<script setup>
import { ref, computed, watch } from 'vue'
const boomNum = Number(localStorage.getItem('boomNum')) || 40
const row = Number(localStorage.getItem('size')) || 20
const col = Number(localStorage.getItem('size')) || 20
const createLayout = () => {
  const preList = new Array(row)
  for (let i = 0; i < row; i++) {
    preList[i] = []
    for (let j = 0; j < col; j++) {
      preList[i][j] = {
        // -1雷 0普通
        type: 0,
        // 0未点开 1点开 2插旗
        state: 0,
        // 周围雷的个数
        num: 0,
        i,
        j
      }
    }
  }
  let copyBoomNum = boomNum
  while (copyBoomNum) {
    const randomRowIndex = Math.floor(Math.random() * row)
    const randomColIndex = Math.floor(Math.random() * col)
    if (preList[randomRowIndex][randomColIndex].type === 0) {
      preList[randomRowIndex][randomColIndex].type = -1
      copyBoomNum--
    }
  }
  return preList
}
const list = ref(createLayout())
const getAroundItemList = (i, j) => {
  const aroundItemList = []
  if (i !== 0 && j !== 0) {
    aroundItemList.push(list.value[i - 1][j - 1])
  }
  if (j !== 0) {
    aroundItemList.push(list.value[i][j - 1])
  }
  if (i !== 0 && j !== col - 1) {
    aroundItemList.push(list.value[i - 1][j + 1])
  }
  if (i !== 0) {
    aroundItemList.push(list.value[i - 1][j])
  }
  if (j !== col - 1) {
    aroundItemList.push(list.value[i][j + 1])
  }
  if (i !== row - 1 && j !== 0) {
    aroundItemList.push(list.value[i + 1][j - 1])
  }
  if (i !== row - 1) {
    aroundItemList.push(list.value[i + 1][j])
  }
  if (i !== row - 1 && j !== col - 1) {
    aroundItemList.push(list.value[i + 1][j + 1])
  }
  return aroundItemList
}
for (let i = 0; i < list.value.length; i++) {
  for (let j = 0; j < list.value[i].length; j++) {
    if (list.value[i][j].type === -1) {
      getAroundItemList(i, j).forEach(item => {
        if (item.type === 0) {
          item.num++
        }
      })
    }
  }
}
const clickItem = (i, j, isUser = true) => {
  // 点到雷
  if (isUser && list.value[i][j].type === -1) {
    alert('GAME OVER');
    window.location.reload()
    return
  }
  // 不是雷
  list.value[i][j].state = 1
  for (const item of getAroundItemList(i, j)) {
    if (item.type === 0 && item.state === 0 && item.num === 0) {
      item.state = 1
      clickItem(item.i, item.j, false)
    } else if (item.type === 0 && item.state === 0) {
      item.state = 1
    }
  }
}
const mark = (e, curItem) => {
  e.preventDefault()
  if (curItem.state === 0) {
    curItem.state = 2
  } else if (curItem.state === 2) {
    curItem.state = 0
  }
}
const computedBoomNum = computed(() => {
  return list.value.flat().reduce((acc, cur) => {
    if (cur.state === 2) {
      acc--
    }
    return acc
  }, boomNum)
})
watch(list.value, (newVal) => {
  let blackNum = 0
  for (const item of newVal.flat()) {
    if (item.state === 0 || item.state === 2) {
      blackNum++
    }
  }
  if (blackNum === boomNum) {
    alert('YOU WIN')
  }
})
const setting = () => {
  const boomNum = prompt('设置地雷数量')
  const size = prompt('设置行列数')
  boomNum && localStorage.setItem('boomNum', boomNum)
  size && localStorage.setItem('size', size)
  window.location.reload()
}
</script>

<template>
  <span @click="setting">修改设置</span>
  <br>
  <span>剩余地雷数：{{computedBoomNum}}</span>
  <div class="layout">
    <div class="row" v-for="(item,index) in list" :key="index">
      <div class="item" v-for="(_item,_index) in item" :key="_index"
        :class="[{show:_item.state===1},{flag:_item.state===2}]" @click="clickItem(index,_index)"
        @contextmenu="mark($event,_item)">
        {{_item.type===-1?'雷':_item.num}}
      </div>
    </div>
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
}

.row {
  display: flex;
}

.item {
  cursor: pointer;
  border: 1px solid gray;
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: black;
  color: transparent;
}

.item.show {
  background-color: transparent;
  color: black;
}

.item.flag {
  background-color: red;
}
</style>
