<template>
  <aside class="menu">
    <div class="menu-header">
      <h1>ツールボックス</h1>
    </div>
    <nav class="menu-nav">
      <ul class="menu-list">
        <li
          v-for="tool in tools"
          :key="tool.id"
          class="menu-item"
          :class="{ active: selectedTool === tool.id }"
          @click="selectTool(tool.id)"
        >
          <span class="menu-icon">{{ tool.icon }}</span>
          <span class="menu-label">{{ tool.name }}</span>
        </li>
      </ul>
    </nav>
  </aside>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  selectedTool: {
    type: String,
    default: null
  },
  tools: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['select'])

const selectTool = (toolId) => {
  emit('select', toolId)
}
</script>

<style scoped>
.menu {
  width: 280px;
  height: 100vh;
  background: linear-gradient(180deg, #667eea 0%, #764ba2 100%);
  color: white;
  display: flex;
  flex-direction: column;
  box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
  position: fixed;
  left: 0;
  top: 0;
  z-index: 1000;
}

.menu-header {
  padding: 24px 20px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.menu-header h1 {
  font-size: 24px;
  font-weight: 700;
  margin: 0;
}

.menu-nav {
  flex: 1;
  overflow-y: auto;
  padding: 16px 0;
}

.menu-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.menu-item {
  padding: 16px 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 12px;
  transition: all 0.2s ease;
  border-left: 3px solid transparent;
}

.menu-item:hover {
  background-color: rgba(255, 255, 255, 0.1);
  border-left-color: rgba(255, 255, 255, 0.5);
}

.menu-item.active {
  background-color: rgba(255, 255, 255, 0.2);
  border-left-color: white;
  font-weight: 600;
}

.menu-icon {
  font-size: 20px;
  width: 24px;
  text-align: center;
}

.menu-label {
  font-size: 16px;
}

/* スクロールバーのスタイル */
.menu-nav::-webkit-scrollbar {
  width: 6px;
}

.menu-nav::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
}

.menu-nav::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 3px;
}

.menu-nav::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.5);
}

/* レスポンシブ対応 */
@media (max-width: 768px) {
  .menu {
    width: 100%;
    height: auto;
    position: relative;
  }

  .menu-nav {
    max-height: 300px;
  }
}
</style>
