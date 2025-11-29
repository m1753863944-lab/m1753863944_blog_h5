<template>
  <div class="typewriter-container">
    <span class="typewriter-text" :style="{ color: textColor }">{{ displayedText }}</span>
    <span class="cursor" :class="{ blinking: isTyping }">|</span>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'

interface Props {
  text: string
  speed?: number
  delay?: number
  textColor?: string
  loop?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  speed: 100,
  delay: 1000,
  textColor: '#ffffff',
  loop: false
})

const displayedText = ref('')
const isTyping = ref(true)
const currentIndex = ref(0)
const isDeleting = ref(false)

const type = () => {
  const fullText = props.text
  
  if (isDeleting.value) {
    // 删除模式
    displayedText.value = fullText.substring(0, currentIndex.value - 1)
    currentIndex.value--
    
    if (currentIndex.value === 0) {
      isDeleting.value = false
      setTimeout(type, props.delay)
      return
    }
  } else {
    // 打字模式
    displayedText.value = fullText.substring(0, currentIndex.value + 1)
    currentIndex.value++
    
    if (currentIndex.value === fullText.length) {
      isTyping.value = false
      
      if (props.loop) {
        setTimeout(() => {
          isDeleting.value = true
          isTyping.value = true
          setTimeout(type, props.delay)
        }, props.delay * 2)
        return
      }
    }
  }
  
  setTimeout(type, isDeleting.value ? props.speed / 2 : props.speed)
}

const startTyping = () => {
  displayedText.value = ''
  currentIndex.value = 0
  isDeleting.value = false
  isTyping.value = true
  setTimeout(type, props.delay)
}

onMounted(() => {
  startTyping()
})

watch(() => props.text, () => {
  startTyping()
})
</script>

<style scoped>
.typewriter-container {
  display: inline-block;
  font-family: 'Courier New', monospace;
  font-size: inherit;
  position: relative;
}

.typewriter-text {
  font-weight: bold;
  text-shadow: 0 0 10px currentColor;
}

.cursor {
  display: inline-block;
  margin-left: 2px;
  color: #ff6b6b;
  font-weight: bold;
  animation: cursorBlink 1s infinite;
}

.cursor.blinking {
  animation: cursorBlink 0.7s infinite;
}

@keyframes cursorBlink {
  0%, 50% {
    opacity: 1;
  }
  51%, 100% {
    opacity: 0;
  }
}

/* 发光效果 */
.typewriter-text {
  position: relative;
}

.typewriter-text::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: currentColor;
  filter: blur(15px);
  opacity: 0.3;
  z-index: -1;
  animation: textGlow 2s ease-in-out infinite alternate;
}

@keyframes textGlow {
  0% {
    opacity: 0.2;
    filter: blur(10px);
  }
  100% {
    opacity: 0.4;
    filter: blur(20px);
  }
}
</style>