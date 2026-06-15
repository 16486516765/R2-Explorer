<template>
  <q-btn 
    dense 
    flat 
    round 
    icon="menu" 
    class="glass-btn q-mr-sm" 
    @click="$emit('toggle')" 
  />

  <q-toolbar-title style="overflow: unset" class="text-bold flex items-center logo-title">
    <q-avatar size="32px" class="q-mr-sm logo-bounce">
      <img src="/logo-white.svg" class="brand-logo">
    </q-avatar>
    <span class="brand-text">R2-Explorer</span>
  </q-toolbar-title>

  <q-space />

  <div v-if="mainStore.buckets.length > 1" class="picker-container">
    <bucket-picker/>
  </div>
</template>

<script>
import BucketPicker from "components/main/BucketPicker.vue";
import { useMainStore } from "stores/main-store";
import { defineComponent } from "vue";

export default defineComponent({
	name: "TopBar",
	emits: ["toggle"],
	components: { BucketPicker },
	setup() {
		const mainStore = useMainStore();
		return { mainStore };
	},
});
</script>

<style scoped>
/* ==========================================
   1. 菜单按钮：谷歌 Material 悬浮微交互
   ========================================== */
.glass-btn {
  color: #333a42;
  background: rgba(0, 0, 0, 0);
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
}

.glass-btn:hover {
  background: rgba(0, 0, 0, 0.06) !important;
  transform: scale(1.05);
}

.glass-btn:active {
  transform: scale(0.95);
}

/* ==========================================
   2. Logo 与标题美化
   ========================================== */
.logo-title {
  user-select: none;
}

/* 顺滑的 Logo 悬浮微动效 */
.logo-bounce {
  transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.logo-title:hover .logo-bounce {
  transform: scale(1.1) rotate(4deg);
}

/* 浅色顶栏下的深色高级文字颜色 */
.brand-text {
  background: linear-gradient(135deg, #1e293b 0%, #475569 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-size: 1.15rem;
  letter-spacing: -0.02em;
}

/* 如果 logo-white 真的太白，可以用 filter 强行把它压暗，或者直接换图片 */
.brand-logo {
  filter: drop-shadow(0 2px 4px rgba(0,0,0,0.05));
}

/* ==========================================
   3. 右侧选择器平滑入场
   ========================================== */
.picker-container {
  animation: slide-in 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes slide-in {
  from {
    opacity: 0;
    transform: translateX(10px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}
</style>
