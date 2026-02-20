<template>
  <div class="base64-tool">
    <div class="tool-description">
      <p>テキストをBase64形式にエンコードしたり、Base64文字列をデコードして元のテキストに戻すことができます。</p>
    </div>

    <div class="tool-container">
      <!-- エンコードセクション -->
      <div class="section">
        <div class="section-header">
          <h3>エンコード（テキスト → Base64）</h3>
          <button 
            class="action-button"
            @click="encodeText"
            :disabled="!encodeInput.trim()"
          >
            エンコード
          </button>
        </div>
        <textarea
          v-model="encodeInput"
          class="textarea"
          placeholder="エンコードしたいテキストを入力してください..."
          rows="6"
        ></textarea>
        <div v-if="encodedResult" class="result-box">
          <div class="result-header">
            <span>結果:</span>
            <button class="copy-button" @click="copyToClipboard(encodedResult, $event)">
              📋 コピー
            </button>
          </div>
          <div class="result-content">{{ encodedResult }}</div>
        </div>
      </div>

      <!-- デコードセクション -->
      <div class="section">
        <div class="section-header">
          <h3>デコード（Base64 → テキスト）</h3>
          <button 
            class="action-button"
            @click="decodeText"
            :disabled="!decodeInput.trim()"
          >
            デコード
          </button>
        </div>
        <textarea
          v-model="decodeInput"
          class="textarea"
          placeholder="デコードしたいBase64文字列を入力してください..."
          rows="6"
        ></textarea>
        <div v-if="decodedResult" class="result-box">
          <div class="result-header">
            <span>結果:</span>
            <button class="copy-button" @click="copyToClipboard(decodedResult, $event)">
              📋 コピー
            </button>
          </div>
          <div class="result-content">{{ decodedResult }}</div>
        </div>
        <div v-if="decodeError" class="error-box">
          <span>⚠️ {{ decodeError }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const encodeInput = ref('')
const encodedResult = ref('')
const decodeInput = ref('')
const decodedResult = ref('')
const decodeError = ref('')

const encodeText = () => {
  try {
    encodedResult.value = btoa(unescape(encodeURIComponent(encodeInput.value)))
  } catch (error) {
    encodedResult.value = 'エンコードエラーが発生しました'
  }
}

const decodeText = () => {
  try {
    decodeError.value = ''
    decodedResult.value = decodeURIComponent(escape(atob(decodeInput.value)))
  } catch (error) {
    decodedResult.value = ''
    decodeError.value = '無効なBase64文字列です。正しい形式を入力してください。'
  }
}

const copyToClipboard = async (text, event) => {
  try {
    await navigator.clipboard.writeText(text)
    // 簡単なフィードバック（実際のアプリではトースト通知などを使うと良い）
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
.base64-tool {
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
  gap: 32px;
}

.section {
  background-color: #fafafa;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.section-header h3 {
  margin: 0;
  font-size: 20px;
  color: #333;
  font-weight: 600;
}

.action-button {
  padding: 10px 24px;
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

.action-button:hover:not(:disabled) {
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(102, 126, 234, 0.4);
}

.action-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
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

.result-box {
  margin-top: 16px;
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
  font-size: 13px;
  line-height: 1.6;
  color: #333;
  max-height: 300px;
  overflow-y: auto;
}

.error-box {
  margin-top: 16px;
  padding: 12px;
  background-color: #fff3cd;
  border: 1px solid #ffc107;
  border-radius: 6px;
  color: #856404;
  font-size: 14px;
}

/* レスポンシブ対応 */
@media (max-width: 768px) {
  .section-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 12px;
  }

  .action-button {
    width: 100%;
  }

  .tool-container {
    gap: 24px;
  }

  .section {
    padding: 16px;
  }
}
</style>
