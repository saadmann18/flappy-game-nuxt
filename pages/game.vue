<template>
  <div class="min-h-screen bg-gradient-to-b from-sky-400 to-sky-600 overflow-hidden relative">
    <!-- Game Canvas -->
    <canvas
      ref="gameCanvas"
      :width="gameWidth"
      :height="gameHeight"
      class="block mx-auto border-2 border-gray-300"
      @click="flap"
    ></canvas>
    
    <!-- Game UI Overlay -->
    <div class="absolute top-4 left-4 text-white">
      <div class="text-2xl font-bold">Score: {{ score }}</div>
      <div class="text-lg">Best: {{ bestScore }}</div>
    </div>
    
    <!-- Game Over Screen -->
    <div v-if="gameState === 'gameOver'" class="absolute inset-0 bg-black/50 flex items-center justify-center">
      <div class="bg-white rounded-lg p-8 text-center space-y-4">
        <h2 class="text-3xl font-bold text-red-600">Game Over!</h2>
        <p class="text-xl">Score: {{ score }}</p>
        <p v-if="score === bestScore" class="text-lg text-yellow-600">🎉 New Best Score!</p>
        <div class="space-x-4">
          <button 
            @click="restartGame"
            class="bg-green-500 hover:bg-green-600 text-white px-6 py-2 rounded font-bold"
          >
            Play Again
          </button>
          <NuxtLink 
            to="/"
            class="bg-gray-500 hover:bg-gray-600 text-white px-6 py-2 rounded font-bold inline-block"
          >
            Home
          </NuxtLink>
        </div>
      </div>
    </div>
    
    <!-- Start Screen -->
    <div v-if="gameState === 'start'" class="absolute inset-0 bg-black/30 flex items-center justify-center">
      <div class="bg-white rounded-lg p-8 text-center space-y-4">
        <h2 class="text-3xl font-bold text-blue-600">Ready to Play?</h2>
        <p class="text-lg">Click anywhere or press SPACEBAR to flap!</p>
        <button 
          @click="startGame"
          class="bg-yellow-400 hover:bg-yellow-500 text-black px-8 py-3 rounded-full font-bold text-xl"
        >
          🎮 Start Game
        </button>
      </div>
    </div>
    
    <!-- Controls Info -->
    <div class="absolute bottom-4 left-4 text-white/80 text-sm">
      <p>Click or SPACEBAR to flap</p>
      <NuxtLink to="/" class="text-blue-200 hover:text-white underline">← Back to Home</NuxtLink>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Bird {
  x: number
  y: number
  velocity: number
  size: number
}

interface Pipe {
  x: number
  topHeight: number
  bottomHeight: number
  width: number
  passed: boolean
}

// Page meta
definePageMeta({
  title: 'Flappy Bird - Game'
})

// Reactive data
const gameCanvas = ref<HTMLCanvasElement | null>(null)
const gameState = ref<'start' | 'playing' | 'gameOver'>('start')
const score = ref(0)
const bestScore = ref(0)

// Game constants
const gameWidth = 800
const gameHeight = 600
const gravity = 0.5
const flapPower = -8
const pipeSpeed = 2
const pipeWidth = 60
const pipeGap = 150

// Game objects
let bird: Bird = {
  x: 100,
  y: gameHeight / 2,
  velocity: 0,
  size: 20
}

let pipes: Pipe[] = []
let ctx: CanvasRenderingContext2D | null = null
let gameLoop: number | null = null

// Load best score from localStorage
onMounted(() => {
  const saved = localStorage.getItem('flappyBirdBest')
  if (saved) {
    bestScore.value = parseInt(saved)
  }
  
  // Set up canvas context
  if (gameCanvas.value) {
    ctx = gameCanvas.value.getContext('2d')
  }
  
  // Add keyboard listener
  window.addEventListener('keydown', handleKeyDown)
})

onUnmounted(() => {
  if (gameLoop) {
    cancelAnimationFrame(gameLoop)
  }
  window.removeEventListener('keydown', handleKeyDown)
})

// Game functions
const handleKeyDown = (event: KeyboardEvent) => {
  if (event.code === 'Space') {
    event.preventDefault()
    if (gameState.value === 'playing') {
      flap()
    } else if (gameState.value === 'start') {
      startGame()
    }
  }
}

const flap = () => {
  if (gameState.value === 'playing') {
    bird.velocity = flapPower
  }
}

const startGame = () => {
  gameState.value = 'playing'
  resetGame()
  if (gameLoop) {
    cancelAnimationFrame(gameLoop)
  }
  gameLoop = requestAnimationFrame(update)
}

const resetGame = () => {
  bird = {
    x: 100,
    y: gameHeight / 2,
    velocity: 0,
    size: 20
  }
  pipes = []
  score.value = 0
  
  // Generate initial pipes
  for (let i = 0; i < 3; i++) {
    generatePipe(gameWidth + i * 250)
  }
}

const generatePipe = (x: number) => {
  const minHeight = 50
  const maxHeight = gameHeight - pipeGap - minHeight
  const topHeight = Math.random() * (maxHeight - minHeight) + minHeight
  
  pipes.push({
    x,
    topHeight,
    bottomHeight: gameHeight - topHeight - pipeGap,
    width: pipeWidth,
    passed: false
  })
}

const update = () => {
  if (gameState.value !== 'playing') return
  
  // Update bird
  bird.velocity += gravity
  bird.y += bird.velocity
  
  // Update pipes
  pipes.forEach(pipe => {
    pipe.x -= pipeSpeed
    
    // Check if bird passed pipe
    if (!pipe.passed && bird.x > pipe.x + pipe.width) {
      pipe.passed = true
      score.value++
    }
  })
  
  // Remove off-screen pipes and add new ones
  pipes = pipes.filter(pipe => pipe.x + pipe.width > -50)
  
  if (pipes.length < 3) {
    const lastPipe = pipes[pipes.length - 1]
    generatePipe(lastPipe.x + 250)
  }
  
  // Check collisions
  if (checkCollisions()) {
    endGame()
    return
  }
  
  draw()
  gameLoop = requestAnimationFrame(update)
}

const checkCollisions = (): boolean => {
  // Ground and ceiling collision
  if (bird.y + bird.size > gameHeight || bird.y < 0) {
    return true
  }
  
  // Pipe collision
  for (const pipe of pipes) {
    if (
      bird.x + bird.size > pipe.x &&
      bird.x < pipe.x + pipe.width &&
      (bird.y < pipe.topHeight || bird.y + bird.size > gameHeight - pipe.bottomHeight)
    ) {
      return true
    }
  }
  
  return false
}

const draw = () => {
  if (!ctx) return
  
  // Clear canvas
  ctx.fillStyle = '#87CEEB'
  ctx.fillRect(0, 0, gameWidth, gameHeight)
  
  // Draw pipes
  ctx.fillStyle = '#228B22'
  pipes.forEach(pipe => {
    // Top pipe
    ctx.fillRect(pipe.x, 0, pipe.width, pipe.topHeight)
    // Bottom pipe
    ctx.fillRect(pipe.x, gameHeight - pipe.bottomHeight, pipe.width, pipe.bottomHeight)
    
    // Pipe caps
    ctx.fillStyle = '#32CD32'
    ctx.fillRect(pipe.x - 5, pipe.topHeight - 20, pipe.width + 10, 20)
    ctx.fillRect(pipe.x - 5, gameHeight - pipe.bottomHeight, pipe.width + 10, 20)
    ctx.fillStyle = '#228B22'
  })
  
  // Draw bird
  ctx.fillStyle = '#FFD700'
  ctx.beginPath()
  ctx.arc(bird.x + bird.size/2, bird.y + bird.size/2, bird.size/2, 0, Math.PI * 2)
  ctx.fill()
  
  // Bird details
  ctx.fillStyle = '#FF6347'
  ctx.beginPath()
  ctx.arc(bird.x + bird.size/2 + 5, bird.y + bird.size/2, 3, 0, Math.PI * 2)
  ctx.fill()
  
  // Eye
  ctx.fillStyle = '#000'
  ctx.beginPath()
  ctx.arc(bird.x + bird.size/2 + 2, bird.y + bird.size/2 - 3, 2, 0, Math.PI * 2)
  ctx.fill()
}

const endGame = () => {
  gameState.value = 'gameOver'
  
  if (score.value > bestScore.value) {
    bestScore.value = score.value
    localStorage.setItem('flappyBirdBest', bestScore.value.toString())
  }
  
  if (gameLoop) {
    cancelAnimationFrame(gameLoop)
  }
}

const restartGame = () => {
  gameState.value = 'start'
}

// Head management
useHead({
  title: 'Flappy Bird - Play Game',
  meta: [
    { name: 'description', content: 'Play the Flappy Bird game' }
  ]
})
</script>