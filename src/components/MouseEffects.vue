<template>
  <div class="mouse-effects">
    <!-- 自定义光标 -->
    <div v-if="isMouseInPage" class="cursor-inner" :style="{ left: mouseX + 'px', top: mouseY + 'px' }">
      <div class="cursor-star">⭐</div>
    </div>
    
    <!-- 鼠标移动轨迹粒子 -->
    <div 
      v-for="(particle, index) in trailParticles" 
      :key="index"
      :style="{
        left: particle.x + 'px',
        top: particle.y + 'px',
        width: particle.size + 'px',
        height: particle.size + 'px',
        background: particle.color,
        transform: `rotate(${particle.rotation}deg) scale(${particle.scale})`
      }"
      class="trail-particle"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

interface TrailParticle {
  x: number
  y: number
  size: number
  color: string
  rotation: number
  scale: number
  life: number
  velocityX: number
  velocityY: number
}

const mouseX = ref(0)
const mouseY = ref(0)
const isMouseInPage = ref(true)
const trailParticles = ref<TrailParticle[]>([])

// 颜色数组
const colors = ['#ff0000', '#00ff00', '#0000ff', '#ffff00', '#ff00ff', '#00ffff', '#ff6b00', '#ff0080', '#8000ff']

// 移除节流，实现零延迟
const handleMouseMove = (event: MouseEvent) => {
  mouseX.value = event.clientX
  mouseY.value = event.clientY
  
  // 创建轨迹粒子（每次移动都创建）
  createTrailParticle(event.clientX, event.clientY)
}

const handleMouseLeave = () => {
  isMouseInPage.value = false
}

const handleMouseEnter = () => {
  isMouseInPage.value = true
}

const createTrailParticle = (x: number, y: number) => {
  // 每次创建3个粒子
  for (let i = 0; i < 3; i++) {
    const color = colors[Math.floor(Math.random() * colors.length)]
    const trail: TrailParticle = {
      x: x + (Math.random() - 0.5) * 20,
      y: y + (Math.random() - 0.5) * 20,
      size: Math.random() * 3 + 2,
      color: color || '#ff0000',
      life: 60,
      velocityX: (Math.random() - 0.5) * 6,
      velocityY: (Math.random() - 0.5) * 6,
      rotation: Math.random() * 360
    }
    
    trailParticles.value.push(trail)
  }
  
  // 限制粒子数量到150个
  if (trailParticles.value.length > 150) {
    trailParticles.value.splice(0, trailParticles.value.length - 150)
  }
}

const updateParticles = () => {
  trailParticles.value = trailParticles.value.map(particle => {
    const newParticle = { ...particle }
    newParticle.life -= 2
    newParticle.x += newParticle.velocityX
    newParticle.y += newParticle.velocityY
    newParticle.rotation += 8
    newParticle.scale *= 0.95
    newParticle.velocityX *= 0.98
    newParticle.velocityY *= 0.98
    return newParticle
  }).filter(particle => particle.life > 0)
  
  requestAnimationFrame(updateParticles)
}

onMounted(() => {
  document.addEventListener('mousemove', handleMouseMove)
  document.addEventListener('mouseleave', handleMouseLeave)
  document.addEventListener('mouseenter', handleMouseEnter)
  updateParticles()
})

onUnmounted(() => {
  document.removeEventListener('mousemove', handleMouseMove)
  document.removeEventListener('mouseleave', handleMouseLeave)
  document.removeEventListener('mouseenter', handleMouseEnter)
})
</script>

<style scoped>
.mouse-effects {
  position: fixed;
  top: 0;
  left: 0;
  pointer-events: none;
  z-index: 9999;
}

.cursor-inner {
  position: fixed;
  width: 32px;
  height: 32px;
  background: transparent;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  /* 移除所有过渡效果，实现零延迟 */
  transition: none;
  z-index: 10000;
  pointer-events: none;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cursor-star {
  font-size: 28px;
  color: #ffd700;
  text-shadow: 
    0 0 10px #ff6b00,
    0 0 20px #ff0080,
    0 0 30px #00ffff;
  animation: starSpin 3s linear infinite, starGlow 2s ease-in-out infinite alternate;
  /* 移除悬停过渡效果 */
  transition: none;
}

/* 悬停效果增强 */
.cursor-inner:hover .cursor-star {
  transform: scale(1.4);
  color: #ff6b00;
  text-shadow: 
    0 0 15px #ff0080,
    0 0 25px #00ffff,
    0 0 35px #ffd700;
  animation: starSpin 1.5s linear infinite, starGlow 1s ease-in-out infinite alternate;
}

@keyframes starSpin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes starGlow {
  0% {
    filter: brightness(1);
    opacity: 0.9;
  }
  100% {
    filter: brightness(1.5);
    opacity: 1;
  }
}

.trail-particle {
  position: absolute;
  border-radius: 50%;
  pointer-events: none;
  animation: particleFade 1s ease-out forwards;
  box-shadow: 0 0 6px currentColor;
  filter: brightness(1.2);
}

@keyframes particleFade {
  0% {
    opacity: 0.8;
  }
  100% {
    opacity: 0;
  }
}

/* 性能优化：减少重绘 */
.trail-particle {
  will-change: transform, opacity;
}
</style>