<template>
  <div class="background-particles">
    <canvas ref="canvas" class="particle-canvas"></canvas>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const canvas = ref<HTMLCanvasElement>()

interface Particle {
  x: number
  y: number
  size: number
  speedX: number
  speedY: number
  color: string
  opacity: number
  life: number
}

const particles = ref<Particle[]>([])
const mouseX = ref(0)
const mouseY = ref(0)
const isMouseMoving = ref(false)

const colors = [
  '#ff6b6b', '#4ecdc4', '#45b7d1', '#feca57', '#ff9ff3', '#54a0ff', '#5f27cd'
]

const initParticles = () => {
  if (!canvas.value) return
  
  const ctx = canvas.value.getContext('2d')
  if (!ctx) return
  
  // 设置canvas大小
  canvas.value.width = window.innerWidth
  canvas.value.height = window.innerHeight
  
  // 创建初始粒子（进一步减少数量提高性能）
  for (let i = 0; i < 30; i++) {
    createParticle()
  }
  
  // 开始动画循环
  animate()
}

const createParticle = () => {
  const color = colors[Math.floor(Math.random() * colors.length)]
  const particle: Particle = {
    x: Math.random() * window.innerWidth,
    y: Math.random() * window.innerHeight,
    size: Math.random() * 3 + 1,
    speedX: (Math.random() - 0.5) * 0.5,
    speedY: (Math.random() - 0.5) * 0.5,
    color: color || '#ff6b6b',
    opacity: Math.random() * 0.5 + 0.2,
    life: Math.random() * 100 + 50
  }
  
  particles.value.push(particle)
}

const animate = () => {
  if (!canvas.value) return
  
  const ctx = canvas.value.getContext('2d')
  if (!ctx) return
  
  // 清除画布
  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height)
  
  // 更新和绘制粒子
  particles.value.forEach((particle, index) => {
    // 更新位置
    particle.x += particle.speedX
    particle.y += particle.speedY
    
    // 边界检查
    if (particle.x < 0 || particle.x > canvas.value!.width) {
      particle.speedX *= -1
    }
    if (particle.y < 0 || particle.y > canvas.value!.height) {
      particle.speedY *= -1
    }
    
    // 鼠标交互 - 增强效果
    if (isMouseMoving.value) {
      const dx = particle.x - mouseX.value
      const dy = particle.y - mouseY.value
      const distance = Math.sqrt(dx * dx + dy * dy)
      
      if (distance < 150) { // 扩大影响范围
        const angle = Math.atan2(dy, dx)
        const force = (150 - distance) / 150
        
        // 增强排斥力
        particle.speedX += Math.cos(angle) * force * 0.3
        particle.speedY += Math.sin(angle) * force * 0.3
        
        // 鼠标附近粒子发光效果
        if (distance < 80) {
          particle.opacity = Math.min(particle.opacity + 0.1, 0.9)
        }
      }
    }
    
    // 绘制粒子
    ctx.beginPath()
    ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2)
    ctx.fillStyle = particle.color
    ctx.globalAlpha = particle.opacity
    ctx.fill()
    
    // 绘制光晕
    const gradient = ctx.createRadialGradient(
      particle.x, particle.y, 0,
      particle.x, particle.y, particle.size * 3
    )
    gradient.addColorStop(0, particle.color)
    gradient.addColorStop(1, 'transparent')
    
    ctx.beginPath()
    ctx.arc(particle.x, particle.y, particle.size * 3, 0, Math.PI * 2)
    ctx.fillStyle = gradient
    ctx.globalAlpha = particle.opacity * 0.3
    ctx.fill()
    
    // 更新生命周期
    particle.life -= 0.5
    if (particle.life <= 0) {
      particles.value.splice(index, 1)
      createParticle()
    }
  })
  
  // 绘制连线
  drawConnections(ctx)
  
  requestAnimationFrame(animate)
}

const drawConnections = (ctx: CanvasRenderingContext2D) => {
  // 优化连线算法，减少计算量
  for (let i = 0; i < particles.value.length; i++) {
    const p1 = particles.value[i]
    if (!p1) continue
    
    // 只检查相邻的粒子，减少循环次数
    for (let j = i + 1; j < Math.min(i + 5, particles.value.length); j++) {
      const p2 = particles.value[j]
      if (!p2) continue
      
      const dx = p1.x - p2.x
      const dy = p1.y - p2.y
      const distanceSquared = dx * dx + dy * dy // 使用平方距离避免开方运算
      
      if (distanceSquared < 22500) { // 150^2 = 22500
        const distance = Math.sqrt(distanceSquared)
        const opacity = 1 - distance / 150
        ctx.beginPath()
        ctx.moveTo(p1.x, p1.y)
        ctx.lineTo(p2.x, p2.y)
        ctx.strokeStyle = p1.color
        ctx.globalAlpha = opacity * 0.2 // 降低透明度
        ctx.lineWidth = 0.3 // 减小线宽
        ctx.stroke()
      }
    }
  }
}

const handleMouseMove = (event: MouseEvent) => {
  mouseX.value = event.clientX
  mouseY.value = event.clientY
  isMouseMoving.value = true
  
  // 创建鼠标移动时的背景特效粒子
  if (Math.random() > 0.7) {
    createMouseInteractionParticle(event.clientX, event.clientY)
  }
  
  // 重置鼠标移动状态
  setTimeout(() => {
    isMouseMoving.value = false
  }, 200) // 延长鼠标移动状态时间
}

const createMouseInteractionParticle = (x: number, y: number) => {
  const color = colors[Math.floor(Math.random() * colors.length)]
  const angle = Math.random() * Math.PI * 2
  const speed = Math.random() * 2 + 0.5
  
  const particle: Particle = {
    x: x + (Math.random() - 0.5) * 20,
    y: y + (Math.random() - 0.5) * 20,
    size: Math.random() * 2 + 0.5,
    speedX: Math.cos(angle) * speed,
    speedY: Math.sin(angle) * speed,
    color: color || '#ff6b6b',
    opacity: Math.random() * 0.8 + 0.2,
    life: Math.random() * 80 + 40
  }
  
  particles.value.push(particle)
  
  // 限制粒子数量
  if (particles.value.length > 40) {
    particles.value.shift()
  }
}

const handleResize = () => {
  if (!canvas.value) return
  
  canvas.value.width = window.innerWidth
  canvas.value.height = window.innerHeight
}

onMounted(() => {
  initParticles()
  window.addEventListener('mousemove', handleMouseMove)
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove)
  window.removeEventListener('resize', handleResize)
})
</script>

<style scoped>
.background-particles {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  z-index: -1;
}

.particle-canvas {
  display: block;
  width: 100%;
  height: 100%;
}
</style>