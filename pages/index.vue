<template>
  <div class="min-h-screen bg-gradient-to-b from-sky-400 to-sky-600 flex flex-col items-center justify-center">
    <div class="text-center space-y-8">
      <h1 class="text-6xl font-bold text-white drop-shadow-lg animate-bounce">
        🐦 Flappy Bird
      </h1>
      
      <div class="space-y-4">
        <p class="text-xl text-white/90 max-w-md mx-auto">
          Navigate through pipes by clicking or pressing spacebar to flap your wings!
        </p>
        
        <div class="flex flex-col items-center space-y-4">
          <NuxtLink 
            to="/game" 
            class="bg-yellow-400 hover:bg-yellow-500 text-black font-bold py-4 px-8 rounded-full text-xl transition-all duration-200 transform hover:scale-105 shadow-lg"
          >
            🎮 Start Game
          </NuxtLink>
          
          <div class="text-white/80 text-sm">
            <p>Controls:</p>
            <p>Click or press SPACEBAR to flap</p>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Floating clouds animation -->
    <div class="absolute top-20 left-10 animate-pulse">
      <div class="text-4xl opacity-70">☁️</div>
    </div>
    <div class="absolute top-32 right-20 animate-pulse delay-1000">
      <div class="text-3xl opacity-60">☁️</div>
    </div>
    <div class="absolute bottom-32 left-1/4 animate-pulse delay-500">
      <div class="text-5xl opacity-50">☁️</div>
    </div>
  </div>
</template>

<script setup lang="ts">
// Page meta
definePageMeta({
  title: 'Flappy Bird - Home'
})

// Head management
useHead({
  title: 'Flappy Bird Game',
  meta: [
    { name: 'description', content: 'Play the classic Flappy Bird game built with Nuxt 3 and TypeScript' }
  ]
})
</script>