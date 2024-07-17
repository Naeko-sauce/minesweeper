<script setup lang="ts">
import { reactive } from 'vue'

const iatticewidth = 10 // 棋盘宽度
const iatticewheight = 10 // 棋盘高度

// 定义方块状态接口
interface BlockState {
  x: number // 方块的 x 坐标
  y: number // 方块的 y 坐标
  reactive: boolean // 方块是否被点开（未使用）
  mine?: boolean // 方块是否是雷
  flagged?: boolean // 方块是否被标记为旗帜（未使用）
  adjocentMines: number // 方块相邻的雷的数量
}

// 创建 10x10 的棋盘，初始状态为相邻雷数为 0
const dota = reactive(
  Array.from({ length: iatticewheight }, (_, y) =>
    Array.from(
      { length: iatticewidth },
      (_, x): BlockState => ({ x, y, adjocentMines: 0, reactive: false }), // 初始化方块状态
    )),
)

// 定义方向数组，用来表示相邻方块的八个方向
const dire = [
  [1, 1], // 右下
  [1, 0], // 右
  [1, -1], // 右上
  [0, -1], // 上
  [-1, -1], // 左上
  [-1, 0], // 左
  [-1, 1], // 左下
  [0, 1], // 下
]

const numberColors = [
  'text-amber',
  'text-blue',
  'text-emerald',
  'text-green',
  'text-fuchsia',
  'text-lime',
  'text-cyan',
  'text-orange',
  'text-shadow-color-blue',
  'text-shadow-color-cyan',
]

// 更新每个方块相邻的雷的数量
function updateNumbers() {
  dota.forEach((row, y) =>
    row.forEach((cell, x) => {
      if (cell.mine)
        return // 如果当前方块是雷，则跳过
      dire.forEach(([dx, dy]) => {
        const x2 = x + dx
        const y2 = y + dy
        if (x2 < 0 || x2 >= iatticewidth || y2 < 0 || y2 >= iatticewheight)
          return // 超出边界则跳过
        if (dota[y2][x2].mine)
          cell.adjocentMines += 1 // 如果相邻方块是雷，则当前方块的相邻雷数量加 1
      })
    }),
  )
}

function boo() {
  for (const row of dota) {
    for (const bl of row)
      bl.mine = Math.random() < 0.3
  }
}

function getBlockClass(b: BlockState) {
  if (!b.reactive)
    return
  return b.mine ? 'bg-red/50' : numberColors[b.adjocentMines]
}

function onClick(b: BlockState) {
  b.reactive = true
}

boo()
updateNumbers()
</script>

<template>
  <div>
    Minesweeper

    <div
      v-for="(row, y) in dota"
      :key="y"
      class="flex items-center justify-center"
    >
      <button
        v-for="(item, x) in row"
        :key="x"
        border="1 gray-400/10"
        m="0.5"
        class="h-10 w-10 flex items-center justify-center border hover:bg-light-50"
        :class="getBlockClass(item)"
        @click="() => onClick(item)"
      >
        <template v-if="item.reactive">
          <div v-if="item.mine" class="i-mdi-mine flex items-center justify-center" />
          <div v-else>
            {{ item.adjocentMines }}
          </div>
        </template>
      </button>
    </div>
  </div>
</template>
