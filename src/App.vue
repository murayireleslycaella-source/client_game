<template>
  <div id="app">
    <GameHUD :score="score" :health="health" :timer="timer" />

    <div class="game-wrapper">
      <GameBoard
        :player="player"
        :hazards="hazards"
        @click="movePlayer"
      />
      <GameOver
        v-if="isGameOver"
        :score="score"
        :highScore="highScore"
        @restart="restartGame"
      />
    </div>

    <p class="hint">Click anywhere on the board to move your character. Dodge the red circles!</p>
  </div>
</template>

<script setup>
import { ref, reactive, watch, onUnmounted } from 'vue'
import GameHUD   from './components/GameHUD.vue'
import GameBoard from './components/GameBoard.vue'
import GameOver  from './components/GameOver.vue'

// ── State ──────────────────────────────────────────────
const score      = ref(0)
const health     = ref(100)
const timer      = ref(0)
const isGameOver = ref(false)
const highScore  = ref(0)
const difficulty = ref(1)

const player  = reactive({ x: 300, y: 250 })
const hazards = ref([])

// ── Internals ──────────────────────────────────────────
let gameLoop  = null
let spawnLoop = null
let timerLoop = null
let rafId     = null
let hazardId  = 0

const BOARD_W  = 800
const BOARD_H  = 500
const HIT_DIST = 26

// ── Start / Restart ────────────────────────────────────
function startGame() {
  score.value      = 0
  health.value     = 100
  timer.value      = 0
  isGameOver.value = false
  difficulty.value = 1
  hazards.value    = []
  player.x         = 300
  player.y         = 250

  gameLoop  = setInterval(() => { score.value++ }, 1000)
  timerLoop = setInterval(() => { timer.value++ }, 1000)
  startSpawner()
  rafId = requestAnimationFrame(gameFrame)
}

// ── Spawner ────────────────────────────────────────────
function startSpawner() {
  clearInterval(spawnLoop)
  const interval = Math.max(400, 1200 - difficulty.value * 100)
  spawnLoop = setInterval(spawnHazard, interval)
}

function spawnHazard() {
  if (isGameOver.value) return
  const side = Math.floor(Math.random() * 4)
  let x, y

  if      (side === 0) { x = Math.random() * BOARD_W; y = 0 }
  else if (side === 1) { x = BOARD_W;                 y = Math.random() * BOARD_H }
  else if (side === 2) { x = Math.random() * BOARD_W; y = BOARD_H }
  else                 { x = 0;                        y = Math.random() * BOARD_H }

  const speed = 1 + difficulty.value * 0.2
  hazards.value.push({
    id: hazardId++,
    x,
    y,
    vx: ((player.x - x) / 80) * speed,
    vy: ((player.y - y) / 80) * speed
  })
}

// ── Game Frame (runs every animation frame) ────────────
function gameFrame() {
  if (!isGameOver.value) {
    // Move all hazards
    hazards.value.forEach(h => {
      h.x += h.vx
      h.y += h.vy
    })
    // Remove hazards that have gone off screen
    hazards.value = hazards.value.filter(
      h => h.x > -50 && h.x < BOARD_W + 50 &&
           h.y > -50 && h.y < BOARD_H + 50
    )
    checkCollisions()
  }
  rafId = requestAnimationFrame(gameFrame)
}

// ── Collision Detection ────────────────────────────────
function checkCollisions() {
  hazards.value = hazards.value.filter(h => {
    const dist = Math.sqrt((h.x - player.x) ** 2 + (h.y - player.y) ** 2)
    if (dist < HIT_DIST) {
      health.value = Math.max(0, health.value - 10)
      if (health.value <= 0) endGame()
      return false // remove the hazard that hit
    }
    return true
  })
}

// ── Player Movement ────────────────────────────────────
function movePlayer(pos) {
  player.x = pos.x
  player.y = pos.y
}

// ── Game Over ──────────────────────────────────────────
function endGame() {
  isGameOver.value = true
  clearInterval(gameLoop)
  clearInterval(spawnLoop)
  clearInterval(timerLoop)
  cancelAnimationFrame(rafId)
  if (score.value > highScore.value) highScore.value = score.value
}

function restartGame() {
  cancelAnimationFrame(rafId)
  clearInterval(gameLoop)
  clearInterval(spawnLoop)
  clearInterval(timerLoop)
  startGame()
}

// ── Difficulty Scaling ─────────────────────────────────
// Every 10 points, increase difficulty and respawn faster
watch(score, val => {
  if (val > 0 && val % 10 === 0) {
    difficulty.value++
    startSpawner()
  }
})

// ── Cleanup when component is destroyed ───────────────
onUnmounted(() => {
  clearInterval(gameLoop)
  clearInterval(spawnLoop)
  clearInterval(timerLoop)
  cancelAnimationFrame(rafId)
})

// ── Kick things off ────────────────────────────────────
startGame()
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  background: #0f0f1a;
  font-family: 'Courier New', monospace;
}

#app {
  max-width: 860px;
  margin: 0 auto;
  padding: 20px;
}

.game-wrapper {
  position: relative;
}

.hint {
  margin-top: 12px;
  color: #666;
  font-size: 0.85rem;
  text-align: center;
}
</style>
