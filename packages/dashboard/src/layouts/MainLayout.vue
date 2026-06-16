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

<style>
.screen-bg {
  position: relative;
  min-height: 100vh;
}

.screen-bg::before {
  content: '';
  position: fixed;
  inset: 0;
  z-index: 0;
  background: 
    radial-gradient(ellipse at 20% 20%, rgba(100, 180, 255, 0.35) 0%, transparent 50%),
    radial-gradient(ellipse at 80% 80%, rgba(180, 140, 255, 0.25) 0%, transparent 50%),
    linear-gradient(135deg, #dce8f5 0%, #e8eaf6 50%, #d8ecd8 100%);
  pointer-events: none;
}

.q-header.glass-header {
  background: rgba(255, 255, 255, 0.55) !important;
  backdrop-filter: blur(24px) saturate(180%) !important;
  -webkit-backdrop-filter: blur(24px) saturate(180%) !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.5);
  box-shadow: 0 2px 20px rgba(0, 0, 0, 0.06);
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.q-drawer.glass-drawer {
  background: rgba(255, 255, 255, 0.4) !important;
  backdrop-filter: blur(24px) saturate(160%) !important;
  -webkit-backdrop-filter: blur(24px) saturate(160%) !important;
  border-right: 1px solid rgba(255, 255, 255, 0.35) !important;
  box-shadow: 4px 0 24px rgba(0, 0, 0, 0.04);
}

.fade-transform-enter-active,
.fade-transform-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}
.fade-transform-enter-from {
  opacity: 0;
  transform: translateY(12px);
}
.fade-transform-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}
</style>
