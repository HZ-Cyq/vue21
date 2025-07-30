<template>
    <div class="color-legend">
      <div class="color-legend__header" @click="toggleCollapse">
        <span class="color-legend__title">热力图颜色说明</span>
        <span class="color-legend__toggle">{{ collapsed ? '展开' : '收起' }}</span>
      </div>
      <transition name="fade">
        <div v-show="!collapsed" class="color-legend__items">
          <div
            v-for="(item, index) in legendData"
            :key="index"
            class="color-legend__item"
            :title="item.label"
          >
            <div
              class="color-legend__color-box"
              :style="{ backgroundColor: item.color }"
            ></div>
            <div class="color-legend__label" :title="item.label">
              {{ item.label }}
            </div>
          </div>
        </div>
      </transition>
    </div>
  </template>
  
  <script>
  export default {
    name: 'ColorLegend',
    data() {
      return {
        collapsed: false,
        legendData: [
          { color: '#0000ff', label: '0 - 20（冷区）' },
          { color: '#00ffff', label: '21 - 40（偏冷）' },
          { color: '#00ff00', label: '41 - 60（适中）' },
          { color: '#ffff00', label: '61 - 80（偏热）' },
          { color: '#ff0000', label: '81 - 100（热区）' }
        ]
      }
    },
    methods: {
      toggleCollapse() {
        this.collapsed = !this.collapsed
      }
    }
  }
  </script>
  
  <style scoped>
  .color-legend {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
    font-size: 14px;
    background-color: #fff;
    border: 1px solid #ddd;
    border-radius: 4px;
    width: fit-content;
    padding: 12px;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  }
  
  .color-legend__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    cursor: pointer;
    font-weight: 600;
    margin-bottom: 8px;
    user-select: none;
  }
  
  .color-legend__toggle {
    font-size: 12px;
    color: #007bff;
  }
  
  .color-legend__items {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }
  
  .color-legend__item {
    display: flex;
    align-items: center;
  }
  
  .color-legend__color-box {
    width: 18px;
    height: 18px;
    border: 1px solid #aaa;
    border-radius: 2px;
    margin-right: 8px;
  }
  
  .color-legend__label {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 180px;
  }
  
  /* 动画过渡 */
  .fade-enter-active,
  .fade-leave-active {
    transition: all 0.3s ease;
  }
  .fade-enter, 
  .fade-leave-to {
    opacity: 0;
    height: 0;
    overflow: hidden;
  }
  </style>
  