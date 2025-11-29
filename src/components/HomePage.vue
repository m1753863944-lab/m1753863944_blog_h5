<template>
  <div class="home-page">
    <!-- 背景特效由BackgroundParticles组件提供 -->
    
    <!-- 导航栏 -->
    <nav class="navbar">
      <div class="nav-container">
        <h1 class="logo">Elish's Blog</h1>
        <div class="nav-links">
          <a href="#home" class="nav-link" @click="scrollToSection('home')">首页</a>
          <a href="#about" class="nav-link" @click="scrollToSection('about')">关于</a>
          <a href="#contact" class="nav-link" @click="scrollToSection('contact')">联系</a>
          <a href="#messages" class="nav-link" @click="scrollToSection('messages')">留言</a>
        </div>
      </div>
    </nav>

    <!-- 主要内容区域 -->
    <main class="main-content">
      <!-- 首页区域 -->
      <section id="home" class="hero-section">
        <div class="hero-content">
          <div class="hero-text">
            <h1 class="hero-title">
              <span class="gradient-text">
                <TypewriterEffect :text="'欢迎来到'" :speed="150" textColor="#ff6b6b" />
              </span>
              <span class="glowing-text">
                <TypewriterEffect :text="'Elish的博客'" :speed="150" :delay="800" textColor="#4ecdc4" />
              </span>
            </h1>
            <p class="hero-subtitle">探索技术、分享生活、记录成长</p>
            <div class="hero-buttons">
              <button class="btn-primary" @click="scrollToSection('about')">了解更多</button>
              <button class="btn-secondary" @click="scrollToSection('messages')">给我留言</button>
            </div>
          </div>
          <div class="hero-image">
            <div class="avatar-container">
              <div class="avatar">E</div>
              <div class="avatar-glow"></div>
            </div>
          </div>
        </div>
      </section>

      <!-- 关于区域 -->
      <section id="about" class="about-section">
        <div class="section-container">
          <h2 class="section-title">关于我</h2>
          <div class="about-content">
            <div class="about-text">
              <p>你好！我是Elish，一名热爱技术的开发者。</p>
              <p>专注于前端开发、用户体验设计和创新技术的探索。</p>
              <p>在这里，我会分享我的学习笔记、项目经验和生活感悟。</p>
            </div>
            <div class="skills">
              <h3>技术栈</h3>
              <div class="skill-tags">
                <span class="skill-tag">Vue.js</span>
                <span class="skill-tag">React</span>
                <span class="skill-tag">TypeScript</span>
                <span class="skill-tag">Node.js</span>
                <span class="skill-tag">Python</span>
                <span class="skill-tag">UI/UX设计</span>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 联系区域 -->
      <section id="contact" class="contact-section">
        <div class="section-container">
          <h2 class="section-title">联系我</h2>
          <div class="contact-content">
            <div class="contact-info">
              <div class="contact-item">
                <span class="contact-icon">📧</span>
                <span class="contact-text">m1753863944@gmail.com</span>
              </div>
              <div class="contact-item">
                <span class="contact-icon">💼</span>
                <span class="contact-text">随时欢迎技术交流与合作</span>
              </div>
            </div>
            <div class="social-links">
              <a href="mailto:m1753863944@gmail.com" class="social-link">
                <span class="social-icon">📧</span>
                <span>发送邮件</span>
              </a>
            </div>
          </div>
        </div>
      </section>

      <!-- 留言区域 -->
      <section id="messages" class="messages-section">
        <div class="section-container">
          <h2 class="section-title">留言板</h2>
          <div class="messages-content">
            <!-- 留言表单 -->
            <div class="message-form">
              <h3>留下你的想法</h3>
              <form @submit.prevent="submitMessage">
                <div class="form-group">
                  <input 
                    v-model="newMessage.name" 
                    type="text" 
                    placeholder="你的名字" 
                    required
                    class="form-input"
                  >
                </div>
                <div class="form-group">
                  <input 
                    v-model="newMessage.email" 
                    type="email" 
                    placeholder="你的邮箱" 
                    required
                    class="form-input"
                  >
                </div>
                <div class="form-group">
                  <textarea 
                    v-model="newMessage.content" 
                    placeholder="留言内容..." 
                    required
                    rows="4"
                    class="form-textarea"
                  ></textarea>
                </div>
                <button type="submit" class="btn-submit">发送留言</button>
              </form>
            </div>

            <!-- 留言列表 -->
            <div class="messages-list">
              <h3>最新留言</h3>
              <div v-if="messages.length === 0" class="no-messages">
                <p>还没有留言，快来成为第一个留言的人吧！</p>
              </div>
              <div v-else>
                <div 
                  v-for="message in messages" 
                  :key="message.id"
                  class="message-item"
                >
                  <div class="message-header">
                    <span class="message-author">{{ message.name }}</span>
                    <span class="message-time">{{ formatTime(message.timestamp) }}</span>
                  </div>
                  <div class="message-content">{{ message.content }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
    </main>

    <!-- 页脚 -->
    <footer class="footer">
      <div class="footer-content">
        <p>&copy; 2024 Elish's Blog. 保留所有权利。</p>
        <p>Made with ❤️ and Vue.js</p>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import TypewriterEffect from './TypewriterEffect.vue'

interface Message {
  id: number
  name: string
  email: string
  content: string
  timestamp: number
}

const messages = ref<Message[]>([])
const newMessage = ref({
  name: '',
  email: '',
  content: ''
})

// 加载留言
const loadMessages = () => {
  const saved = localStorage.getItem('elish-blog-messages')
  if (saved) {
    messages.value = JSON.parse(saved)
  }
}

// 提交留言
const submitMessage = () => {
  const message: Message = {
    id: Date.now(),
    name: newMessage.value.name,
    email: newMessage.value.email,
    content: newMessage.value.content,
    timestamp: Date.now()
  }
  
  messages.value.unshift(message)
  localStorage.setItem('elish-blog-messages', JSON.stringify(messages.value))
  
  // 清空表单
  newMessage.value = { name: '', email: '', content: '' }
  
  // 显示成功提示（可以添加Toast组件）
  alert('留言发送成功！')
}

// 格式化时间
const formatTime = (timestamp: number) => {
  return new Date(timestamp).toLocaleString('zh-CN')
}

// 滚动到指定区域
const scrollToSection = (sectionId: string) => {
  const element = document.getElementById(sectionId)
  if (element) {
    element.scrollIntoView({ behavior: 'smooth' })
  }
}

onMounted(() => {
  loadMessages()
  
  // 添加滚动动画效果
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('animate-in')
      }
    })
  }, { threshold: 0.1 })
  
  document.querySelectorAll('.section-container').forEach(section => {
    observer.observe(section)
  })
})
</script>

<style scoped>
.home-page {
  min-height: 100vh;
  position: relative;
}

/* 背景特效由BackgroundParticles组件提供，已删除重复样式 */

/* 导航栏样式 */
.navbar {
  position: fixed;
  top: 0;
  width: 100%;
  background: rgba(26, 26, 46, 0.9);
  backdrop-filter: blur(10px);
  z-index: 1000;
  padding: 1rem 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 2rem;
}

.logo {
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-size: 1.5rem;
  font-weight: bold;
}

.nav-links {
  display: flex;
  gap: 2rem;
}

.nav-link {
  color: #ffffff;
  text-decoration: none;
  padding: 0.5rem 1rem;
  border-radius: 25px;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.nav-link::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  transition: left 0.3s ease;
  z-index: -1;
}

.nav-link:hover::before {
  left: 0;
}

.nav-link:hover {
  color: #000;
  transform: translateY(-2px);
}

/* 主要内容区域 */
.main-content {
  padding-top: 80px;
}

/* 英雄区域 */
.hero-section {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
}

.hero-content {
  max-width: 1200px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 4rem;
  align-items: center;
}

.hero-title {
  font-size: 3.5rem;
  margin-bottom: 1rem;
  line-height: 1.2;
}

.gradient-text {
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-size: 200% 200%;
  animation: gradient 3s ease infinite;
}

.glowing-text {
  color: #ffffff;
  text-shadow: 0 0 20px rgba(255, 107, 107, 0.7);
  animation: glow-text 2s ease-in-out infinite alternate;
}

@keyframes gradient {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

@keyframes glow-text {
  0% { text-shadow: 0 0 20px rgba(255, 107, 107, 0.7); }
  100% { text-shadow: 0 0 30px rgba(78, 205, 196, 0.9), 0 0 40px rgba(255, 107, 107, 0.5); }
}

.hero-subtitle {
  font-size: 1.3rem;
  margin-bottom: 2rem;
  color: #cccccc;
}

.hero-buttons {
  display: flex;
  gap: 1rem;
}

.btn-primary, .btn-secondary, .btn-submit {
  padding: 0.8rem 2rem;
  border: none;
  border-radius: 25px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.btn-primary {
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  color: white;
}

.btn-secondary {
  background: transparent;
  color: #ffffff;
  border: 2px solid #4ecdc4;
}

.btn-primary:hover, .btn-secondary:hover, .btn-submit:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
}

.avatar-container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
}

.avatar {
  width: 200px;
  height: 200px;
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 4rem;
  color: white;
  font-weight: bold;
  position: relative;
  z-index: 2;
  animation: rotate 10s linear infinite;
}

.avatar-glow {
  position: absolute;
  width: 250px;
  height: 250px;
  background: radial-gradient(circle, rgba(255, 107, 107, 0.3) 0%, transparent 70%);
  border-radius: 50%;
  animation: pulse 2s ease-in-out infinite alternate;
}

@keyframes rotate {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

@keyframes pulse {
  0% { transform: scale(1); opacity: 0.5; }
  100% { transform: scale(1.1); opacity: 0.8; }
}

/* 通用区域样式 */
.section-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 4rem 2rem;
  opacity: 0;
  transform: translateY(50px);
  transition: all 0.6s ease;
}

.section-container.animate-in {
  opacity: 1;
  transform: translateY(0);
}

.section-title {
  font-size: 2.5rem;
  text-align: center;
  margin-bottom: 3rem;
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

/* 关于区域 */
.about-content {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 3rem;
  align-items: start;
}

.about-text p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: 1rem;
  color: #cccccc;
}

.skills h3 {
  margin-bottom: 1rem;
  color: #ffffff;
}

.skill-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.skill-tag {
  background: rgba(255, 107, 107, 0.2);
  color: #ff6b6b;
  padding: 0.3rem 0.8rem;
  border-radius: 15px;
  font-size: 0.9rem;
  border: 1px solid rgba(255, 107, 107, 0.3);
}

/* 联系区域 */
.contact-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 3rem;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 1rem;
  padding: 1rem;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 10px;
  transition: all 0.3s ease;
}

.contact-item:hover {
  background: rgba(255, 255, 255, 0.1);
  transform: translateX(10px);
}

.contact-icon {
  font-size: 1.5rem;
}

.social-links {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.social-link {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 1rem;
  background: rgba(78, 205, 196, 0.1);
  border-radius: 10px;
  color: #4ecdc4;
  text-decoration: none;
  transition: all 0.3s ease;
  border: 1px solid rgba(78, 205, 196, 0.3);
}

.social-link:hover {
  background: rgba(78, 205, 196, 0.2);
  transform: translateY(-2px);
}

/* 留言区域 */
.messages-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 3rem;
}

.message-form, .messages-list {
  background: rgba(255, 255, 255, 0.05);
  padding: 2rem;
  border-radius: 15px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.message-form h3, .messages-list h3 {
  margin-bottom: 1.5rem;
  color: #ffffff;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-input, .form-textarea {
  width: 100%;
  padding: 0.8rem;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 8px;
  color: #ffffff;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.form-input:focus, .form-textarea:focus {
  outline: none;
  border-color: #4ecdc4;
  box-shadow: 0 0 10px rgba(78, 205, 196, 0.3);
}

.form-input::placeholder, .form-textarea::placeholder {
  color: #cccccc;
}

.btn-submit {
  background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
  color: white;
  width: 100%;
}

.message-item {
  background: rgba(255, 255, 255, 0.03);
  padding: 1rem;
  margin-bottom: 1rem;
  border-radius: 8px;
  border-left: 3px solid #4ecdc4;
}

.message-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 0.5rem;
}

.message-author {
  color: #4ecdc4;
  font-weight: bold;
}

.message-time {
  color: #888;
  font-size: 0.8rem;
}

.message-content {
  color: #cccccc;
  line-height: 1.5;
}

.no-messages {
  text-align: center;
  color: #888;
  padding: 2rem;
}

/* 页脚 */
.footer {
  background: rgba(0, 0, 0, 0.3);
  padding: 2rem;
  text-align: center;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.footer-content p {
  margin: 0.5rem 0;
  color: #888;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .hero-content,
  .about-content,
  .contact-content,
  .messages-content {
    grid-template-columns: 1fr;
    gap: 2rem;
  }
  
  .hero-title {
    font-size: 2.5rem;
  }
  
  .nav-links {
    gap: 1rem;
  }
  
  .nav-container {
    padding: 0 1rem;
  }
}
</style>