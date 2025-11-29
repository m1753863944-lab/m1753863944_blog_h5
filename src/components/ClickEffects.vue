<template>
  <div class="click-effects">
    <!-- 点击粒子效果 -->
    <div 
      v-for="(explosion, index) in explosions" 
      :key="'explosion-' + index"
      :style="{
        left: explosion.x + 'px',
        top: explosion.y + 'px',
        width: explosion.size + 'px',
        height: explosion.size + 'px',
        background: explosion.color,
        transform: `rotate(${explosion.rotation}deg) translate(${explosion.translateX}px, ${explosion.translateY}px)`
      }"
      class="explosion-particle"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

interface Explosion {
  x: number
  y: number
  size: number
  color: string
  rotation: number
  life: number
  translateX: number
  translateY: number
  velocityX: number
  velocityY: number
}

const explosions = ref<Explosion[]>([])
const isMouseDown = ref(false)
let mouseDownTimer: number | null = null

// 颜色数组 - 更鲜艳的爆炸颜色
const colors = ['#ff0000', '#00ff00', '#0000ff', '#ffff00', '#ff00ff', '#00ffff', '#ff6b00', '#ff0080', '#8000ff']

const handleMouseDown = (event: MouseEvent) => {
  if (event.button === 0) { // 左键
    isMouseDown.value = true
    // 立即触发一次特效
    createEffects(event.clientX, event.clientY)
    // 开始定时器，每100ms触发一次特效
    mouseDownTimer = window.setInterval(() => {
      if (isMouseDown.value) {
        createEffects(event.clientX, event.clientY)
      }
    }, 100)
  }
}

const handleMouseUp = (event: MouseEvent) => {
  if (event.button === 0) { // 左键
    isMouseDown.value = false
    if (mouseDownTimer) {
      clearInterval(mouseDownTimer)
      mouseDownTimer = null
    }
  }
}

const createEffects = (x: number, y: number) => {
  // 创建轨迹小粒子效果（类似鼠标轨迹）
  createTrailParticles(x, y)
}

const createTrailParticles = (x: number, y: number) => {
  const particleCount = Math.floor(Math.random() * 20) + 30 // 增加粒子数量
  
  for (let i = 0; i < particleCount; i++) {
    const color = colors[Math.floor(Math.random() * colors.length)]
    // 全方位扩散，角度范围在 0° 到 360° 之间
    const angle = Math.random() * Math.PI * 2
    const speed = Math.random() * 10 + 6 // 增加速度范围
    
    const particle: Explosion = {
      x: x,
      y: y,
      size: Math.random() * 4 + 2, // 小粒子尺寸
      color: color || '#ff0000',
      rotation: Math.random() * 360,
      life: 80, // 缩短生命周期，更接近轨迹粒子
      translateX: 0,
      translateY: 0,
      velocityX: Math.cos(angle) * speed,
      velocityY: Math.sin(angle) * speed // 全方位扩散
    }
    
    explosions.value.push(particle)
  }
  
  // 增加粒子数量限制，支持连续点击
  if (explosions.value.length > 150) {
    explosions.value.splice(0, explosions.value.length - 150)
  }
}

const updateEffects = () => {
  // 更新粒子效果 - 类似轨迹粒子的行为
  explosions.value = explosions.value.map(particle => {
    const newParticle = { ...particle }
    newParticle.life -= 3
    newParticle.translateX += newParticle.velocityX
    newParticle.translateY += newParticle.velocityY
    newParticle.rotation += 12
    newParticle.size *= 0.92
    // 添加轻微的重力效果，让运动更自然
    newParticle.velocityY += 0.15
    newParticle.velocityX *= 0.96
    newParticle.velocityY *= 0.96
    return newParticle
  }).filter(particle => particle.life > 0)
  
  requestAnimationFrame(updateEffects)
}

onMounted(() => {
  document.addEventListener('mousedown', handleMouseDown)
  document.addEventListener('mouseup', handleMouseUp)
  updateEffects()
})

onUnmounted(() => {
  document.removeEventListener('mousedown', handleMouseDown)
  document.removeEventListener('mouseup', handleMouseUp)
  if (mouseDownTimer) {
    clearInterval(mouseDownTimer)
  }
})
</script>

<style scoped>
.click-effects {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  z-index: 9998;
}

.explosion-particle {
  position: absolute;
  border-radius: 50%;
  pointer-events: none;
  animation: trailFade 0.8s ease-out forwards;
  box-shadow: 0 0 8px currentColor;
  filter: brightness(1.3) contrast(1.2);
  will-change: transform, opacity;
}

@keyframes trailFade {
  0% {
    transform: scale(1) rotate(0deg) translate(0, 0);
    opacity: 0.9;
  }
  100% {
    transform: scale(0.1) rotate(360deg) translate(var(--translate-x), var(--translate-y));
    opacity: 0;
  }
}

/* 悬停增强效果 */
.explosion-particle:hover {
  filter: brightness(1.6) contrast(1.4);
  box-shadow: 0 0 12px currentColor;
}
</style>