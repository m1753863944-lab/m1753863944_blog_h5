# Elish Blog - 个人博客网站

一个现代化的个人博客网站，基于Vue 3 + TypeScript + Vite构建，具有丰富的交互特效和动画效果。

## ✨ 特色功能

### 🖱️ 鼠标交互特效
- **零延迟鼠标样式**：星星图标跟随鼠标，无延迟流畅体验
- **左键点击特效**：小粒子轨迹风格，每次点击生成30-50个粒子
- **鼠标离开检测**：智能检测鼠标离开页面，自动清理特效

### 🌟 背景粒子系统
- **动态粒子动画**：随机运动的粒子背景
- **鼠标交互**：粒子跟随鼠标移动并产生连线效果
- **性能优化**：粒子数量智能控制，保持流畅体验

### ⌨️ 打字机效果
- **欢迎文字动画**：逐字显示欢迎信息
- **循环播放**：动画完成后自动重新开始

## 🚀 快速开始

### 环境要求
- Node.js 16.0+
- npm 或 yarn

### 安装依赖
```sh
npm install
```

### 开发模式
```sh
npm run dev
```
访问 http://localhost:3000/

### 构建生产版本
```sh
npm run build
```

## 📁 项目结构

```
src/
├── components/
│   ├── BackgroundParticles.vue    # 背景粒子特效
│   ├── ClickEffects.vue           # 点击特效
│   ├── MouseEffects.vue           # 鼠标样式特效
│   ├── TypewriterEffect.vue       # 打字机效果
│   └── HomePage.vue               # 主页组件
├── App.vue                        # 根组件
├── main.ts                        # 入口文件
└── router/
    └── index.ts                   # 路由配置
```

## 🛠️ 技术栈

- **前端框架**: Vue 3 + TypeScript
- **构建工具**: Vite 7.2.4
- **样式**: CSS3 + 动画
- **路由**: Vue Router

## 🎯 特效配置

### 鼠标特效配置
- 粒子尺寸：2-6px
- 粒子数量：每次点击30-50个
- 轨迹粒子：每次移动生成3个
- 总量限制：150个粒子

### 背景粒子配置
- 粒子数量：50个
- 连线距离：150px
- 移动速度：随机速度
- 颜色主题：蓝色系渐变

## 🌐 部署

### GitHub Pages
项目已配置为支持GitHub Pages部署，代码位于：
https://github.com/m1753863944-lab/m1753863944_blog_h5

### 其他部署方式
项目支持多种静态网站托管服务：
- Vercel
- Netlify
- Cloudflare Pages

## 📝 开发说明

### 添加新特效
1. 在 `src/components/` 目录下创建新的Vue组件
2. 在 `App.vue` 中引入并使用组件
3. 确保特效性能优化，避免影响页面流畅度

### 自定义样式
- 主要样式在组件内部通过 `<style scoped>` 定义
- 全局样式可在 `App.vue` 中定义
- 支持响应式设计，适配移动端

## 🤝 贡献

欢迎提交Issue和Pull Request来改进项目！

## 📄 许可证

MIT License