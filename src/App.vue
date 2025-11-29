<script setup lang="ts">
import { ref, onMounted } from 'vue'
import HomePage from './components/HomePage.vue'
import MouseEffects from './components/MouseEffects.vue'
import ClickEffects from './components/ClickEffects.vue'
import BackgroundParticles from './components/BackgroundParticles.vue'

// 全局特效状态
const showCursorTrail = ref(true)
</script>

<template>
  <div id="app" class="dark-theme">
    <!-- 背景粒子效果 -->
    <BackgroundParticles />
    
    <!-- 鼠标效果组件 -->
    <MouseEffects v-if="showCursorTrail" />
    
    <!-- 点击效果组件 -->
    <ClickEffects />
    
    <!-- 主内容区域 -->
    <HomePage />
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  overflow-x: hidden;
  cursor: none;
}

.dark-theme {
  background: linear-gradient(135deg, #0c0c0c 0%, #1a1a2e 50%, #16213e 100%);
  min-height: 100vh;
  color: #ffffff;
  position: relative;
}

/* 自定义滚动条 */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #1a1a2e;
}

::-webkit-scrollbar-thumb {
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(45deg, #ff5252, #26a69a);
}
</style>
