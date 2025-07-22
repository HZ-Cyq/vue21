<template>
  <div id="app" class="layout">
    <!-- 左侧菜单 -->
    <div class="sidebar">
      <HoverTreeMenu :nodes="menuData" @component-selected="loadComponent" />
    </div>

    <div class="main">
      <!-- 动态组件面板 -->
      <div class="plugin-panel" v-if="currentComponent">
        <component :is="currentComponent.component" :key="currentComponent.key" />
      </div>
    </div>
  </div>
</template>

<script>
import HoverTreeMenu from "@/components/hovertreemenus/HoverTreeMenu.vue";
export default {
  name: 'App',
  components: {
    HoverTreeMenu
  },
  data() {
    return {
      menuData: [
        {
          title: "测试菜单",
          subMenu: [
            { title: "DemoComponent1", componentName: "DemoComponent1" },
          ],
        },
      ],
      currentComponent: null,
    }
  },
  methods: {
    async loadComponent(name) {
      const componentMap = {
        DemoComponent1: () => import("@/components/hovertreemenus/DemoComponent1.vue")
      };

      try {
        if (!componentMap[name]) throw new Error("组件未注册：" + name);
        const mod = await componentMap[name]();
        this.currentComponent = {
          component: mod.default,
          key: name + "-" + Date.now(),
        };
      } catch (e) {
        alert("加载组件失败：" + name);
        console.error(e);
      }
    },
  }
}
</script>

<style scoped>
.layout {
  display: flex;
  height: 100vh;
  overflow: hidden;
}

.sidebar {
  width: 240px;
  background-color: #2c3e50;
  overflow-y: auto;
}
.main {
  flex: 1;
  position: relative;
}
.plugin-panel {
  position: absolute;
  top: 20px;
  right: 20px;
  width: 300px;
  background: white;
  border: 1px solid #ccc;
  padding: 10px;
  z-index: 999;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}
</style>
