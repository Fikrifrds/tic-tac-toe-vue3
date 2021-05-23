<template>
  <h1>{{ title }}</h1>
  <div class="score" v-if="state.winner">
    <p>Winner</p>
    <p class="winner">{{ state.winner }}</p>
    <button @click="restartGame">Restart Game</button>
  </div>
  <p class="turn" v-else><span class="bold">{{ state.turn }}</span> turn</p>
  <div class="board" :class="{disabled: state.winner}">
    <div @click="clickTile(item)" class="tile" v-for="item in 9" :key="item">
      <span class="fill">{{ state.fill[item] ? state.fill[item] : '' }}</span>
    </div>
  </div>
</template>

<script setup>
import { defineProps, reactive, onMounted } from 'vue'
const winningLines = ['123', '147', '456', '258', '369', '789', '159', '357']

defineProps({
  title: String
})

const state = reactive({
  fill: {},
  turn: 'X',
  winner: null,
  checker: {},
  move: 0,
})

const clickTile = (item) => {
  if (state.fill[item]) return;
  if (state.winner) return; 
  findWinner(item)
  state.fill[item]=state.turn;
  state.turn = state.turn === 'X' ? 'O' : 'X';
  state.move++;
}

const initGame = () => {
  for (const line of winningLines) {
    state.checker[line] = [null,null,null];
  }
}

const restartGame = () => {
  state.fill = {}
  state.turn = 'X'
  state.winner = null
  state.checker = {}
  state.move = 0
  initGame()
}

const findWinner = (item) => {
  const lines = winningLines.filter(line => line.includes(item))
  for (const line of lines) {
    const index = line.indexOf(item);
    const possibleLine = state.checker[line];
    possibleLine[index] = state.turn;
    const winScenario = [state.turn, state.turn, state.turn];
    if (JSON.stringify(possibleLine) === JSON.stringify(winScenario)) {
      return state.winner = state.turn;
    }
  }
  if (state.move === 8) {
    state.winner = 'Draw'
  }
}

onMounted(() => {
  initGame();
})

</script>

<style scoped>
a {
  color: #42b983;
}

.board {
  width: 306px;
  height: 306px;
  margin-left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-wrap: wrap;
}


.bold {
  font-weight: bold;
}

.disabled {
  opacity: 0.5;
  z-index: -1
}

button {
  margin-bottom: 20px;
}

.tile {
  border: 1px solid lightslategray;
  width: 100px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.fill, .winner {
  font-weight: bold;
  font-size: 30px;
}

.turn {
  font-size: 24px;
}
</style>
