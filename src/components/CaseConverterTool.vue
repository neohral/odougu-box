<template>
  <div class="case-converter-tool">
    <div class="tool-description">
      <p>テキストを大文字や小文字に変換できます。大文字、小文字、先頭大文字、タイトルケースなどに対応しています。</p>
    </div>

    <div class="tool-container">
      <div class="input-section">
        <label class="label">変換するテキスト:</label>
        <textarea
          v-model="inputText"
          class="textarea"
          placeholder="変換したいテキストを入力してください..."
          rows="6"
        ></textarea>
      </div>

      <div class="buttons-section">
        <button class="convert-button" @click="convertToUpperCase" :disabled="!inputText.trim()">
          大文字に変換
        </button>
        <button class="convert-button" @click="convertToLowerCase" :disabled="!inputText.trim()">
          小文字に変換
        </button>
        <button class="convert-button" @click="convertToTitleCase" :disabled="!inputText.trim()">
          タイトルケース
        </button>
        <button class="convert-button" @click="convertToSentenceCase" :disabled="!inputText.trim()">
          文の先頭のみ大文字
        </button>
        <button class="convert-button" @click="toggleCase" :disabled="!inputText.trim()">
          大文字小文字を反転
        </button>
      </div>

      <div v-if="result" class="result-section">
        <div class="result-header">
          <span>変換結果:</span>
          <button class="copy-button" @click="copyToClipboard(result, $event)">
            📋 コピー
          </button>
        </div>
        <div class="result-content">{{ result }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const inputText = ref('')
const result = ref('')

const convertToUpperCase = () => {
  result.value = inputText.value.toUpperCase()
}

const convertToLowerCase = () => {
  result.value = inputText.value.toLowerCase()
}

const convertToTitleCase = () => {
  result.value = inputText.value
    .toLowerCase()
    .split(' ')
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(' ')
}

const convertToSentenceCase = () => {
  const text = inputText.value.toLowerCase()
  result.value = text.charAt(0).toUpperCase() + text.slice(1)
}

const toggleCase = () => {
  result.value = inputText.value
    .split('')
    .map(char => {
      if (char === char.toUpperCase()) {
        return char.toLowerCase()
      } else {
        return char.toUpperCase()
      }
    })
    .join('')
}

const copyToClipboard = async (text, event) => {
  try {
    await navigator.clipboard.writeText(text)
    const button = event.target
    const originalText = button.textContent
    button.textContent = '✓ コピーしました'
    setTimeout(() => {
      button.textContent = originalText
    }, 2000)
  } catch (error) {
    alert('コピーに失敗しました')
  }
}
</script>

<style scoped>
.case-converter-tool {
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

.buttons-section {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.convert-button {
  padding: 10px 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 2px 4px rgba(102, 126, 234, 0.3);
}

.convert-button:hover:not(:disabled) {
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(102, 126, 234, 0.4);
}

.convert-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.result-section {
  margin-top: 8px;
  padding: 16px;
  background-color: #ffffff;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
}

.result-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  font-weight: 600;
  color: #333;
}

.copy-button {
  padding: 6px 12px;
  background-color: #f0f0f0;
  border: 1px solid #d0d0d0;
  border-radius: 4px;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.copy-button:hover {
  background-color: #e0e0e0;
}

.result-content {
  word-break: break-all;
  white-space: pre-wrap;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 14px;
  line-height: 1.6;
  color: #333;
  min-height: 60px;
}

@media (max-width: 768px) {
  .buttons-section {
    flex-direction: column;
  }

  .convert-button {
    width: 100%;
  }
}
</style>
