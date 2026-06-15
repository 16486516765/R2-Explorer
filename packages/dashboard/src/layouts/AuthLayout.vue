<template>
  <q-layout view="lHh lPr lFf" class="auth-bg flex flex-center">
    
    <div class="glow-sphere glow-1"></div>
    <div class="glow-sphere glow-2"></div>

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
/* ==========================================
   1. 现代科技感/流光渐变背景
   ========================================== */
.auth-bg {
  position: relative;
  width: 100vw;
  height: 100vh;
  background: linear-gradient(135deg, #e0e6ed 0%, #f4f7f6 50%, #e8ecf3 100%);
  overflow: hidden;
}

/* ==========================================
   2. 仿 iOS 26 质感的光晕气泡（在毛玻璃后方隐约透出）
   ========================================== */
.glow-sphere {
  position: absolute;
  border-radius: 50%;
  filter: blur(100px);
  opacity: 0.45;
  z-index: 0;
  pointer-events: none;
}

.glow-1 {
  width: 400px;
  height: 400px;
  background: radial-gradient(circle, #8ac7db 0%, rgba(138, 199, 219, 0) 70%);
  top: -10%;
  left: -5%;
  animation: float-slow 12s ease-in-out infinite alternate;
}

.glow-2 {
  width: 500px;
  height: 500px;
  background: radial-gradient(circle, #a2ded0 0%, rgba(162, 222, 208, 0) 70%);
  bottom: -10%;
  right: -5%;
  animation: float-slow 16s ease-in-out infinite alternate-reverse;
}

/* ==========================================
   3. 核心容器与超强毛玻璃效果 (Glassmorphism)
   ========================================== */
.auth-container {
  position: relative;
  z-index: 1; /* 确保在光晕上方 */
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  max-width: 440px; /* 适配手机和PC的黄金登录框宽度 */
  padding: 24px;
}

/* 提示：如果你的具体登录组件（如 LoginPage.vue）本身带了背景，
   建议把具体登录组件里的卡片背景改成 transparent (透明)，
   或者直接应用以下这个高级毛玻璃样式 */
:deep(.q-card), :deep(.auth-card) {
  background: rgba(255, 255, 255, 0.5) !important;
  backdrop-filter: blur(25px) saturate(120%);
  -webkit-backdrop-filter: blur(25px) saturate(120%);
  border: 1px solid rgba(255, 255, 255, 0.5) !important;
  border-radius: 20px !important; /* 更加圆润的外观 */
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.05), 
              0 5px 15px rgba(0, 0, 0, 0.02) !important;
}

/* ==========================================
   4. 谷歌 Material 动效：丝滑淡入并微微放大
   ========================================== */
.auth-fade-enter-active {
  transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1); /* 带有轻微弹性吸附感的贝塞尔曲线 */
}
.auth-fade-leave-active {
  transition: all 0.3s cubic-bezier(0.25, 1, 0.5, 1);
}

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
