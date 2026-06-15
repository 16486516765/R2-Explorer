<template>
  <q-layout view="hHh LpR lFr" class="screen-bg">

    <q-header reveal class="glass-header text-grey-9">
      <q-toolbar class="q-px-md">

        <top-bar @toggle="toggleLeftDrawer"/>

      </q-toolbar>
    </q-header>

    <q-drawer 
      :width="110" 
      show-if-above 
      v-model="leftDrawerOpen" 
      side="left" 
      class="glass-drawer"
    >
      <left-sidebar/>
    </q-drawer>

    <q-page-container class="main-container">
      <transition name="fade-transform" mode="out-in">
        <router-view />
      </transition>
    </q-page-container>

  </q-layout>
</template>

<script>
import LeftSidebar from "components/main/LeftSidebar.vue";
import RightSidebar from "components/main/RightSidebar.vue";
import TopBar from "components/main/Topbar.vue";
import { ref } from "vue";

export default {
	name: "MainLayout",
	components: { TopBar, RightSidebar, LeftSidebar },
	setup() {
		const leftDrawerOpen = ref(false);
		const rightDrawerOpen = ref(false);

		return {
			leftDrawerOpen,
			toggleLeftDrawer() {
				leftDrawerOpen.value = !leftDrawerOpen.value;
			},

			rightDrawerOpen,
			updateRightDrawer(state) {
				rightDrawerOpen.value = state;
			},
		};
	},
	mounted() {
		this.updateRightDrawer(false);
	},
};
</script>

<style scoped>
/* 1. 页面整体背景：浅灰到微蓝的渐变，衬托出毛玻璃的通透感 */
.screen-bg {
  background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
}

/* 2. 顶部导航栏毛玻璃（iOS/现代网感） */
.glass-header {
  background: rgba(255, 255, 255, 0.65) !important;
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.4);
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.03);
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

/* 3. 左侧侧边栏毛玻璃 */
.glass-drawer {
  background: rgba(255, 255, 255, 0.45) !important;
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-right: 1px solid rgba(255, 255, 255, 0.3) !important;
  box-shadow: 4px 0 30px rgba(0, 0, 0, 0.02);
}

/* 4. 仿谷歌/iOS 的内容区平滑淡入动效 (fade-transform) */
.main-container {
  padding-top: 64px; /* 确保上方留出空间 */
}

.fade-transform-enter-active,
.fade-transform-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1); /* 采用 Material Design 标准贝塞尔曲线 */
}

.fade-transform-enter-from {
  opacity: 0;
  transform: translateY(12px); /* 从下方微微升起 */
}

.fade-transform-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}
</style>
