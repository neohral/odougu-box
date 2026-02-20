<template>
  <div class="regex-checker-tool">
    <div class="tool-description">
      <p>正規表現パターンとテスト文字列を入力して、マッチするかどうかを確認できます。マッチした部分をハイライト表示します。</p>
    </div>

    <div class="tool-container">
      <div class="input-section">
        <label class="label">正規表現パターン:</label>
        <div class="regex-input-wrapper">
          <span class="regex-prefix">/</span>
          <input
            v-model="pattern"
            class="regex-input"
            type="text"
            placeholder="例: [0-9]+"
            @input="checkRegex"
          />
          <span class="regex-suffix">/</span>
          <select v-model="flags" class="flags-select" @change="checkRegex">
            <option value="">フラグなし</option>
            <option value="g">g (グローバル)</option>
            <option value="i">i (大文字小文字無視)</option>
            <option value="m">m (複数行)</option>
            <option value="gi">gi (グローバル + 大文字小文字無視)</option>
            <option value="gm">gm (グローバル + 複数行)</option>
            <option value="im">im (大文字小文字無視 + 複数行)</option>
            <option value="gim">gim (すべて)</option>
          </select>
        </div>
      </div>

      <div class="input-section">
        <label class="label">テスト文字列:</label>
        <textarea
          v-model="testString"
          class="textarea"
          placeholder="テストしたい文字列を入力してください..."
          rows="8"
          @input="checkRegex"
        ></textarea>
      </div>

      <div v-if="pattern || testString" class="result-section">
        <div v-if="error" class="error-box">
          <span>⚠️ {{ error }}</span>
        </div>
        <div v-else-if="pattern && testString" class="match-info">
          <div class="match-status" :class="{ matched: isMatch, 'no-match': !isMatch }">
            <span v-if="isMatch">✓ マッチしました</span>
            <span v-else>✗ マッチしませんでした</span>
          </div>
          <div v-if="matches.length > 0" class="matches-list">
            <div class="matches-header">マッチした箇所 ({{ matches.length }}件):</div>
            <div v-for="(match, index) in matches" :key="index" class="match-item">
              <span class="match-index">#{{ index + 1 }}</span>
              <span class="match-text">{{ match }}</span>
            </div>
          </div>
          <div v-if="highlightedText" class="highlighted-section">
            <div class="highlighted-header">ハイライト表示:</div>
            <div class="highlighted-content" v-html="highlightedText"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const pattern = ref('')
const testString = ref('')
const flags = ref('')
const isMatch = ref(false)
const matches = ref([])
const error = ref('')
const highlightedText = ref('')

const checkRegex = () => {
  error.value = ''
  matches.value = []
  isMatch.value = false
  highlightedText.value = ''

  if (!pattern.value || !testString.value) {
    return
  }

  try {
    const regex = new RegExp(pattern.value, flags.value)
    const globalRegex = new RegExp(pattern.value, flags.value + 'g')
    
    // マッチチェック
    isMatch.value = regex.test(testString.value)

    // すべてのマッチを取得
    if (isMatch.value) {
      const allMatches = testString.value.match(globalRegex)
      if (allMatches) {
        matches.value = allMatches
      }

      // ハイライト表示
      highlightedText.value = testString.value.replace(
        globalRegex,
        (match) => `<mark class="highlight">${escapeHtml(match)}</mark>`
      )
    }
  } catch (e) {
    error.value = e.message
  }
}

const escapeHtml = (text) => {
  const div = document.createElement('div')
  div.textContent = text
  return div.innerHTML
}

watch([pattern, testString, flags], () => {
  checkRegex()
})
</script>

<style scoped>
.regex-checker-tool {
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

.regex-input-wrapper {
  display: flex;
  align-items: center;
  gap: 8px;
}

.regex-prefix,
.regex-suffix {
  font-size: 18px;
  font-weight: 600;
  color: #667eea;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
}

.regex-input {
  flex: 1;
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  transition: border-color 0.2s ease;
}

.regex-input:focus {
  outline: none;
  border-color: #667eea;
}

.flags-select {
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  background-color: white;
  cursor: pointer;
  transition: border-color 0.2s ease;
}

.flags-select:focus {
  outline: none;
  border-color: #667eea;
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

.result-section {
  margin-top: 8px;
}

.error-box {
  padding: 12px;
  background-color: #fff3cd;
  border: 1px solid #ffc107;
  border-radius: 6px;
  color: #856404;
  font-size: 14px;
}

.match-info {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.match-status {
  padding: 12px 16px;
  border-radius: 6px;
  font-weight: 600;
  font-size: 16px;
}

.match-status.matched {
  background-color: #d4edda;
  color: #155724;
  border: 1px solid #c3e6cb;
}

.match-status.no-match {
  background-color: #f8d7da;
  color: #721c24;
  border: 1px solid #f5c6cb;
}

.matches-list {
  padding: 16px;
  background-color: #f8f9fa;
  border-radius: 8px;
}

.matches-header {
  font-weight: 600;
  margin-bottom: 12px;
  color: #333;
}

.match-item {
  padding: 8px;
  margin-bottom: 8px;
  background-color: white;
  border-radius: 4px;
  display: flex;
  align-items: center;
  gap: 12px;
}

.match-index {
  font-weight: 600;
  color: #667eea;
  min-width: 40px;
}

.match-text {
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  color: #333;
}

.highlighted-section {
  padding: 16px;
  background-color: #ffffff;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
}

.highlighted-header {
  font-weight: 600;
  margin-bottom: 12px;
  color: #333;
}

.highlighted-content {
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 14px;
  line-height: 1.6;
  color: #333;
  white-space: pre-wrap;
  word-break: break-all;
}

.highlighted-content :deep(.highlight) {
  background-color: #ffeb3b;
  padding: 2px 4px;
  border-radius: 3px;
  font-weight: 600;
}

@media (max-width: 768px) {
  .regex-input-wrapper {
    flex-wrap: wrap;
  }

  .regex-input {
    width: 100%;
  }

  .flags-select {
    width: 100%;
  }
}
</style>
