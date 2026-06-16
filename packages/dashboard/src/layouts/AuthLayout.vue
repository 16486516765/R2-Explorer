<template>
  <q-layout view="lHh lPr lFf" class="auth-bg">
    
    <!-- 光晕背景层，移到 layout 外部通过固定定位实现 -->
    <div class="glow-sphere glow-1"></div>
    <div class="glow-sphere glow-2"></div>
    <div class="glow-sphere glow-3"></div>

    <q-page-container class="auth-container">
      <transition name="auth-fade" mode="out-in" appear>
        <router-view />
      </transition>
    </q-page-container>

  </q-layout>
</template>

<script>
export default {
  name: "AuthLayout"
};
</script>

<style scoped>
/* 背景 */
.auth-bg {
  position: relative;
  width: 100vw;
  height: 100vh;
  background: linear-gradient(135deg, #dce8f5 0%, #f0f4f8 50%, #e8eaf6 100%);
  overflow: hidden;
}

/* 光晕气泡 - 用 fixed 定位确保在所有层下方可见 */
.glow-sphere {
  position: fixed;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.6;
  z-index: 0;
  pointer-events: none;
}

.glow-1 {
  width: 500px;
  height: 500px;
  background: radial-gradient(circle, rgba(100, 180, 255, 0.7) 0%, transparent 70%);
  top: -15%;
  left: -10%;
  animation: float-slow 12s ease-in-out infinite alternate;
}

.glow-2 {
  width: 600px;
  height: 600px;
  background: radial-gradient(circle, rgba(160, 220, 200, 0.6) 0%, transparent 70%);
  bottom: -15%;
  right: -10%;
  animation: float-slow 16s ease-in-out infinite alternate-reverse;
}

.glow-3 {
  width: 350px;
  height: 350px;
  background: radial-gradient(circle, rgba(200, 160, 255, 0.5) 0%, transparent 70%);
  top: 40%;
  left: 50%;
  animation: float-slow 20s ease-in-out infinite alternate;
}

/* 内容容器 - 确保在光晕上方 */
.auth-container {
  position: relative;
  z-index: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

/* 登录卡片毛玻璃 */
:deep(.q-card),
:deep(.auth-card) {
  background: rgba(255, 255, 255, 0.45) !important;
  backdrop-filter: blur(30px) saturate(150%) !important;
  -webkit-backdrop-filter: blur(30px) saturate(150%) !important;
  border: 1px solid rgba(255, 255, 255, 0.55) !important;
  border-radius: 24px !important;
  box-shadow: 
    0 20px 40px rgba(0, 0, 0, 0.08),
    0 1px 0 rgba(255, 255, 255, 0.6) inset !important;
}

/* 页面动画 */
.auth-fade-enter-active {
  transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
}
.auth-fade-leave-active {
  transition: all 0.3s cubic-bezier(0.25, 1, 0.5, 1);
}
.auth-fade-enter-from {
  opacity: 0;
  transform: scale(0.95) translateY(12px);
}
.auth-fade-leave-to {
  opacity: 0;
  transform: scale(0.98) translateY(-8px);
}

/* 光晕漂浮动画 */
@keyframes float-slow {
  0%   { transform: translate(0, 0) scale(1); }
  100% { transform: translate(25px, 20px) scale(1.08); }
}
</style>
.auth-fade-enter-from {
  opacity: 0;
  transform: scale(0.96) translateY(10px); /* 伴随一点点缩放和位移 */
}
.auth-fade-leave-to {
  opacity: 0;
  transform: scale(0.98) translateY(-10px);
}

/* 慢速漂浮动画，让背景的光晕活起来 */
@keyframes float-slow {
  0% { transform: translate(0, 0) scale(1); }
  100% { transform: translate(30px, 20px) scale(1.1); }
}
</style>
