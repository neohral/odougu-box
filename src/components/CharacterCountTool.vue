<template>
  <div class="character-count-tool">
    <div class="tool-description">
      <p>入力したテキストの文字数をリアルタイムでカウントします。文字数、バイト数、行数、単語数など、様々な統計情報を表示します。</p>
    </div>

    <div class="tool-container">
      <div class="input-section">
        <label class="label">テキストを入力:</label>
        <textarea
          v-model="inputText"
          class="textarea"
          placeholder="文字数をカウントしたいテキストを入力してください..."
          rows="10"
        ></textarea>
      </div>

      <div class="stats-section">
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-label">文字数（空白含む）</div>
            <div class="stat-value">{{ totalCharacters }}</div>
          </div>
          <div class="stat-card">
            <div class="stat-label">文字数（空白除く）</div>
            <div class="stat-value">{{ charactersWithoutSpaces }}</div>
          </div>
          <div class="stat-card">
            <div class="stat-label">バイト数（UTF-8）</div>
            <div class="stat-value">{{ byteCount }}</div>
          </div>
          <div class="stat-card">
            <div class="stat-label">行数</div>
            <div class="stat-value">{{ lineCount }}</div>
          </div>
          <div class="stat-card">
            <div class="stat-label">単語数</div>
            <div class="stat-value">{{ wordCount }}</div>
          </div>
          <div class="stat-card">
            <div class="stat-label">段落数</div>
            <div class="stat-value">{{ paragraphCount }}</div>
          </div>
        </div>
      </div>

      <div v-if="inputText" class="details-section">
        <h3 class="details-header">詳細情報</h3>
        <div class="details-grid">
          <div class="detail-item">
            <span class="detail-label">空白文字:</span>
            <span class="detail-value">{{ spaceCount }}</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">改行文字:</span>
            <span class="detail-value">{{ newlineCount }}</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">タブ文字:</span>
            <span class="detail-value">{{ tabCount }}</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">数字:</span>
            <span class="detail-value">{{ digitCount }}</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">英字:</span>
            <span class="detail-value">{{ letterCount }}</span>
          </div>
          <div class="detail-item">
            <span class="detail-label">記号:</span>
            <span class="detail-value">{{ symbolCount }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const inputText = ref('')

const totalCharacters = computed(() => {
  return inputText.value.length
})

const charactersWithoutSpaces = computed(() => {
  return inputText.value.replace(/\s/g, '').length
})

const byteCount = computed(() => {
  return new TextEncoder().encode(inputText.value).length
})

const lineCount = computed(() => {
  if (!inputText.value) return 0
  return inputText.value.split('\n').length
})

const wordCount = computed(() => {
  if (!inputText.value.trim()) return 0
  // 日本語と英語の単語をカウント
  const japaneseWords = inputText.value.match(/[\u3040-\u309F\u30A0-\u30FF\u4E00-\u9FAF]+/g) || []
  const englishWords = inputText.value.match(/[a-zA-Z]+/g) || []
  return japaneseWords.length + englishWords.length
})

const paragraphCount = computed(() => {
  if (!inputText.value.trim()) return 0
  return inputText.value.split(/\n\s*\n/).filter(p => p.trim()).length
})

const spaceCount = computed(() => {
  return (inputText.value.match(/ /g) || []).length
})

const newlineCount = computed(() => {
  return (inputText.value.match(/\n/g) || []).length
})

const tabCount = computed(() => {
  return (inputText.value.match(/\t/g) || []).length
})

const digitCount = computed(() => {
  return (inputText.value.match(/\d/g) || []).length
})

const letterCount = computed(() => {
  return (inputText.value.match(/[a-zA-Z]/g) || []).length
})

const symbolCount = computed(() => {
  return inputText.value.replace(/[\w\s\u3040-\u309F\u30A0-\u30FF\u4E00-\u9FAF]/g, '').length
})
</script>

<style scoped>
.character-count-tool {
  width: 100%;
}

.tool-description {
  margin-bottom: 32px;
  padding: 16px;
  background-color: #f8f9fa;
  border-radius: 8px;
  border-left: 4px solid #667eea;
}

.tool-description p {
  margin: 0;
  color: #666;
  line-height: 1.6;
}

.tool-container {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.input-section {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.label {
  font-weight: 600;
  color: #333;
  font-size: 14px;
}

.textarea {
  width: 100%;
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  resize: vertical;
  transition: border-color 0.2s ease;
}

.textarea:focus {
  outline: none;
  border-color: #667eea;
}

.stats-section {
  margin-top: 8px;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
}

.stat-card {
  padding: 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 12px;
  color: white;
  box-shadow: 0 4px 6px rgba(102, 126, 234, 0.2);
  transition: transform 0.2s ease;
}

.stat-card:hover {
  transform: translateY(-2px);
}

.stat-label {
  font-size: 13px;
  opacity: 0.9;
  margin-bottom: 8px;
}

.stat-value {
  font-size: 32px;
  font-weight: 700;
  line-height: 1;
}

.details-section {
  margin-top: 8px;
  padding: 20px;
  background-color: #f8f9fa;
  border-radius: 8px;
}

.details-header {
  font-size: 18px;
  font-weight: 600;
  color: #333;
  margin-bottom: 16px;
}

.details-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 12px;
}

.detail-item {
  display: flex;
  justify-content: space-between;
  padding: 12px;
  background-color: white;
  border-radius: 6px;
  border: 1px solid #e0e0e0;
}

.detail-label {
  font-size: 14px;
  color: #666;
}

.detail-value {
  font-size: 16px;
  font-weight: 600;
  color: #333;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
}

@media (max-width: 768px) {
  .stats-grid {
    grid-template-columns: 1fr;
  }

  .details-grid {
    grid-template-columns: 1fr;
  }
}
</style>
