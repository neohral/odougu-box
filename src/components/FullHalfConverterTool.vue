<template>
  <div class="full-half-converter-tool">
    <div class="tool-description">
      <p>全角文字と半角文字を相互に変換できます。英数字、記号、スペース、カタカナの変換に対応しています。</p>
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
        <button class="convert-button" @click="convertToFullWidth" :disabled="!inputText.trim()">
          全角に変換
        </button>
        <button class="convert-button" @click="convertToHalfWidth" :disabled="!inputText.trim()">
          半角に変換
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

// 全角カタカナ→半角カタカナ変換マップ
const createKatakanaMap = () => {
  const map = {}
  // 全角カタカナ（ア-ン）
  const fullKatakana = 'アイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメモヤユヨラリルレロワヲン'
  const halfKatakana = 'ｱｲｳｴｵｶｷｸｹｺｻｼｽｾｿﾀﾁﾂﾃﾄﾅﾆﾇﾈﾉﾊﾋﾌﾍﾎﾏﾐﾑﾒﾓﾔﾕﾖﾗﾘﾙﾚﾛﾜｦﾝ'
  
  for (let i = 0; i < fullKatakana.length; i++) {
    map[fullKatakana[i]] = halfKatakana[i]
  }
  
  // 全角小文字カタカナ（ァ-ン）
  const fullSmallKatakana = 'ァィゥェォッャュョヮヵヶ'
  const halfSmallKatakana = 'ｧｨｩｪｫｯｬｭｮヮヵヶ'
  
  for (let i = 0; i < fullSmallKatakana.length; i++) {
    map[fullSmallKatakana[i]] = halfSmallKatakana[i]
  }
  
  // 濁点・半濁点付きカタカナ
  const dakutenMap = {
    'ガ': 'ｶﾞ', 'ギ': 'ｷﾞ', 'グ': 'ｸﾞ', 'ゲ': 'ｹﾞ', 'ゴ': 'ｺﾞ',
    'ザ': 'ｻﾞ', 'ジ': 'ｼﾞ', 'ズ': 'ｽﾞ', 'ゼ': 'ｾﾞ', 'ゾ': 'ｿﾞ',
    'ダ': 'ﾀﾞ', 'ヂ': 'ﾁﾞ', 'ヅ': 'ﾂﾞ', 'デ': 'ﾃﾞ', 'ド': 'ﾄﾞ',
    'バ': 'ﾊﾞ', 'ビ': 'ﾋﾞ', 'ブ': 'ﾌﾞ', 'ベ': 'ﾍﾞ', 'ボ': 'ﾎﾞ',
    'パ': 'ﾊﾟ', 'ピ': 'ﾋﾟ', 'プ': 'ﾌﾟ', 'ペ': 'ﾍﾟ', 'ポ': 'ﾎﾟ',
    'ヴ': 'ｳﾞ'
  }
  
  Object.assign(map, dakutenMap)
  
  return map
}

// 全角→半角変換マップ
const fullToHalfMap = {
  '０': '0', '１': '1', '２': '2', '３': '3', '４': '4',
  '５': '5', '６': '6', '７': '7', '８': '8', '９': '9',
  'Ａ': 'A', 'Ｂ': 'B', 'Ｃ': 'C', 'Ｄ': 'D', 'Ｅ': 'E',
  'Ｆ': 'F', 'Ｇ': 'G', 'Ｈ': 'H', 'Ｉ': 'I', 'Ｊ': 'J',
  'Ｋ': 'K', 'Ｌ': 'L', 'Ｍ': 'M', 'Ｎ': 'N', 'Ｏ': 'O',
  'Ｐ': 'P', 'Ｑ': 'Q', 'Ｒ': 'R', 'Ｓ': 'S', 'Ｔ': 'T',
  'Ｕ': 'U', 'Ｖ': 'V', 'Ｗ': 'W', 'Ｘ': 'X', 'Ｙ': 'Y',
  'Ｚ': 'Z',
  'ａ': 'a', 'ｂ': 'b', 'ｃ': 'c', 'ｄ': 'd', 'ｅ': 'e',
  'ｆ': 'f', 'ｇ': 'g', 'ｈ': 'h', 'ｉ': 'i', 'ｊ': 'j',
  'ｋ': 'k', 'ｌ': 'l', 'ｍ': 'm', 'ｎ': 'n', 'ｏ': 'o',
  'ｐ': 'p', 'ｑ': 'q', 'ｒ': 'r', 'ｓ': 's', 'ｔ': 't',
  'ｕ': 'u', 'ｖ': 'v', 'ｗ': 'w', 'ｘ': 'x', 'ｙ': 'y',
  'ｚ': 'z',
  '　': ' ', '！': '!', '？': '?', '（': '(', '）': ')',
  '［': '[', '］': ']', '｛': '{', '｝': '}', '：': ':',
  '；': ';', '，': ',', '．': '.', '／': '/', '＼': '\\',
  'ー': 'ｰ', '～': '~', '＠': '@', '＃': '#', '＄': '$',
  '％': '%', '＾': '^', '＆': '&', '＊': '*', '＋': '+',
  '＝': '=', '｜': '|', '＜': '<', '＞': '>', '「': '"',
  '」': '"', '『': "'", '』': "'",
  // カタカナマップを追加
  ...createKatakanaMap()
}

// 半角→全角変換マップ（カタカナも含む）
const createHalfToFullKatakanaMap = () => {
  const map = {}
  // 半角カタカナ→全角カタカナ
  const halfKatakana = 'ｱｲｳｴｵｶｷｸｹｺｻｼｽｾｿﾀﾁﾂﾃﾄﾅﾆﾇﾈﾉﾊﾋﾌﾍﾎﾏﾐﾑﾒﾓﾔﾕﾖﾗﾘﾙﾚﾛﾜｦﾝ'
  const fullKatakana = 'アイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメモヤユヨラリルレロワヲン'
  
  for (let i = 0; i < halfKatakana.length; i++) {
    map[halfKatakana[i]] = fullKatakana[i]
  }
  
  // 半角小文字カタカナ
  const halfSmallKatakana = 'ｧｨｩｪｫｯｬｭｮ'
  const fullSmallKatakana = 'ァィゥェォッャュョ'
  
  for (let i = 0; i < halfSmallKatakana.length; i++) {
    map[halfSmallKatakana[i]] = fullSmallKatakana[i]
  }
  
  // 濁点・半濁点付き半角カタカナ
  const dakutenHalfToFull = {
    'ｶﾞ': 'ガ', 'ｷﾞ': 'ギ', 'ｸﾞ': 'グ', 'ｹﾞ': 'ゲ', 'ｺﾞ': 'ゴ',
    'ｻﾞ': 'ザ', 'ｼﾞ': 'ジ', 'ｽﾞ': 'ズ', 'ｾﾞ': 'ゼ', 'ｿﾞ': 'ゾ',
    'ﾀﾞ': 'ダ', 'ﾁﾞ': 'ヂ', 'ﾂﾞ': 'ヅ', 'ﾃﾞ': 'デ', 'ﾄﾞ': 'ド',
    'ﾊﾞ': 'バ', 'ﾋﾞ': 'ビ', 'ﾌﾞ': 'ブ', 'ﾍﾞ': 'ベ', 'ﾎﾞ': 'ボ',
    'ﾊﾟ': 'パ', 'ﾋﾟ': 'ピ', 'ﾌﾟ': 'プ', 'ﾍﾟ': 'ペ', 'ﾎﾟ': 'ポ',
    'ｳﾞ': 'ヴ'
  }
  
  Object.assign(map, dakutenHalfToFull)
  
  return map
}

// 半角→全角変換マップ
const halfToFullMap = {
  ...Object.fromEntries(
    Object.entries(fullToHalfMap).map(([full, half]) => [half, full])
  ),
  // カタカナマップを追加
  ...createHalfToFullKatakanaMap(),
  // 長音記号
  'ｰ': 'ー'
}

const convertToFullWidth = () => {
  let text = inputText.value
  let converted = ''
  let i = 0
  
  while (i < text.length) {
    const char = text[i]
    const nextChar = text[i + 1]
    
    // 濁点・半濁点の処理（半角カタカナの濁点は2文字で表現される）
    if (nextChar === 'ﾞ' || nextChar === 'ﾟ') {
      const combined = char + nextChar
      if (halfToFullMap[combined]) {
        converted += halfToFullMap[combined]
        i += 2
        continue
      }
    }
    
    // 通常の文字変換
    converted += halfToFullMap[char] || char
    i++
  }
  
  result.value = converted
}

const convertToHalfWidth = () => {
  let text = inputText.value
  // 濁点・半濁点付きカタカナを先に処理
  text = text.replace(/[ガギグゲゴ]/g, char => fullToHalfMap[char] || char)
  text = text.replace(/[ザジズゼゾ]/g, char => fullToHalfMap[char] || char)
  text = text.replace(/[ダヂヅデド]/g, char => fullToHalfMap[char] || char)
  text = text.replace(/[バビブベボ]/g, char => fullToHalfMap[char] || char)
  text = text.replace(/[パピプペポ]/g, char => fullToHalfMap[char] || char)
  text = text.replace(/ヴ/g, char => fullToHalfMap[char] || char)
  
  result.value = text
    .split('')
    .map(char => fullToHalfMap[char] || char)
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
.full-half-converter-tool {
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
