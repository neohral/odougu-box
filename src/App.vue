<template>
  <div class="app">
    <Menu
      :selected-tool="selectedTool"
      :tools="tools"
      @select="handleToolSelect"
    />
    <main class="main-content">
      <div class="content-wrapper">
        <div v-if="!selectedTool" class="welcome-screen">
          <h2>ツールを選択してください</h2>
          <p>左側のメニューから使用したいツールを選択してください。</p>
        </div>
        <div v-else class="tool-content">
          <h2>{{ currentTool?.name }}</h2>
          <Base64Tool v-if="selectedTool === 'base64'" />
          <CaseConverterTool v-else-if="selectedTool === 'case-converter'" />
          <FullHalfConverterTool v-else-if="selectedTool === 'full-half-converter'" />
          <RegexCheckerTool v-else-if="selectedTool === 'regex-checker'" />
          <CharacterCountTool v-else-if="selectedTool === 'character-count'" />
          <div v-else>
            <p>{{ currentTool?.description }}</p>
            <!-- 他のツールのコンテンツがここに表示されます -->
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import Menu from './components/Menu.vue'
import Base64Tool from './components/Base64Tool.vue'
import CaseConverterTool from './components/CaseConverterTool.vue'
import FullHalfConverterTool from './components/FullHalfConverterTool.vue'
import RegexCheckerTool from './components/RegexCheckerTool.vue'
import CharacterCountTool from './components/CharacterCountTool.vue'

const selectedTool = ref(null)

const tools = ref([
  {
    id: 'case-converter',
    name: '大文字・小文字変換',
    icon: '🔤',
    description: 'テキストを大文字や小文字に変換できます。'
  },
  {
    id: 'full-half-converter',
    name: '全角・半角変換',
    icon: '🔄',
    description: '全角文字と半角文字を相互に変換できます。'
  },
  {
    id: 'regex-checker',
    name: '正規表現チェック',
    icon: '🔍',
    description: '正規表現パターンとテスト文字列を入力して、マッチするかどうかを確認できます。'
  },
  {
    id: 'character-count',
    name: '文字数取得',
    icon: '📊',
    description: '入力したテキストの文字数をリアルタイムでカウントします。'
  },
  {
    id: 'base64',
    name: 'Base64エンコード/デコード',
    icon: '🔐',
    description: 'テキストをBase64形式にエンコードしたり、Base64文字列をデコードします。'
  },
])

const currentTool = computed(() => {
  return tools.value.find(tool => tool.id === selectedTool.value)
})

const handleToolSelect = (toolId) => {
  selectedTool.value = toolId
}
</script>

<style scoped>
.app {
  display: flex;
  width: 100%;
  min-height: 100vh;
}

.main-content {
  flex: 1;
  margin-left: 280px;
  min-height: 100vh;
  background-color: #ffffff;
}

.content-wrapper {
  padding: 40px;
  max-width: 1200px;
  margin: 0 auto;
}

.welcome-screen {
  text-align: center;
  padding: 80px 20px;
  color: #666;
}

.welcome-screen h2 {
  font-size: 32px;
  margin-bottom: 16px;
  color: #333;
}

.welcome-screen p {
  font-size: 18px;
  line-height: 1.6;
}

.tool-content {
  animation: fadeIn 0.3s ease-in;
}

.tool-content h2 {
  font-size: 28px;
  margin-bottom: 16px;
  color: #333;
}

.tool-content p {
  font-size: 16px;
  line-height: 1.6;
  color: #666;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* レスポンシブ対応 */
@media (max-width: 768px) {
  .main-content {
    margin-left: 0;
  }

  .content-wrapper {
    padding: 20px;
  }

  .welcome-screen {
    padding: 40px 20px;
  }

  .welcome-screen h2 {
    font-size: 24px;
  }

  .welcome-screen p {
    font-size: 16px;
  }
}
</style>
