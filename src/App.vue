<script setup lang="ts">
import { ref } from 'vue'

const inputText = ref('')
const resultText = ref('')
const currentMode = ref<'encode' | 'decode' | null>(null)

const encodeUrl = () => {
  try {
    resultText.value = encodeURIComponent(inputText.value)
    currentMode.value = 'encode'
  } catch (error) {
    resultText.value = '编码失败'
    currentMode.value = 'encode'
  }
}

const decodeUrl = () => {
  try {
    resultText.value = decodeURIComponent(inputText.value)
    currentMode.value = 'decode'
  } catch (error) {
    resultText.value = '解码失败'
    currentMode.value = 'decode'
  }
}

const clearAll = () => {
  inputText.value = ''
  resultText.value = ''
  currentMode.value = null
}

const copyToClipboard = (text: string) => {
  navigator.clipboard.writeText(text).then(() => {
    alert('已复制到剪贴板')
  })
}
</script>

<template>
  <div class="container">
    <h1>URL 编码解码工具</h1>
    
    <div class="main-content">
      <!-- 左侧输入区域 -->
      <div class="input-panel">
        <div class="panel-header">
          <h2>输入</h2>
        </div>
        <div class="panel-content">
          <textarea 
            v-model="inputText" 
            placeholder="请输入需要编码或解码的文本..."
            rows="12"
            class="input-textarea"
          ></textarea>
          
          <div class="button-group">
            <button @click="encodeUrl" :disabled="!inputText" class="encode-btn">
              🔒 URL 编码
            </button>
            <button @click="decodeUrl" :disabled="!inputText" class="decode-btn">
              🔓 URL 解码
            </button>
            <button @click="clearAll" class="clear-btn">
              🗑️ 清空
            </button>
          </div>
        </div>
      </div>

      <!-- 右侧结果区域 -->
      <div class="result-panel" :class="{ 'has-result': currentMode }">
        <div class="panel-header" :class="`${currentMode}-header`">
          <h2 v-if="currentMode === 'encode'">🔒 编码结果</h2>
          <h2 v-else-if="currentMode === 'decode'">🔓 解码结果</h2>
          <h2 v-else>结果</h2>
        </div>
        <div class="panel-content">
          <textarea 
            v-if="currentMode"
            readonly 
            :value="resultText" 
            rows="12"
            :class="`result-textarea ${currentMode}-result`"
          ></textarea>
          <div v-else class="empty-state">
            <div class="empty-icon">📄</div>
            <p>点击编码或解码按钮查看结果</p>
          </div>
          
          <button 
            v-if="currentMode && resultText" 
            @click="copyToClipboard(resultText)" 
            class="copy-btn"
            :class="`${currentMode}-copy`"
          >
            📋 复制结果
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

h1 {
  text-align: center;
  color: white;
  margin-bottom: 30px;
  font-size: 2.5rem;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
}

.main-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

/* 面板通用样式 */
.input-panel,
.result-panel {
  background: white;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.1);
  overflow: hidden;
  transition: all 0.3s ease;
}

.panel-header {
  padding: 20px;
  font-weight: bold;
  text-align: center;
  color: white;
  background: linear-gradient(45deg, #2c3e50, #34495e);
}

.panel-header h2 {
  margin: 0;
  font-size: 1.3rem;
}

.panel-content {
  padding: 20px;
}

/* 输入面板样式 */
.input-panel {
  border: 3px solid #3498db;
}

.input-textarea {
  width: 100%;
  padding: 15px;
  border: 2px solid #ecf0f1;
  border-radius: 8px;
  font-size: 14px;
  resize: vertical;
  font-family: 'Courier New', monospace;
  background: #fafafa;
  transition: all 0.3s ease;
  box-sizing: border-box;
  rows: 8;
  min-height: 120px;
}

.input-textarea:focus {
  outline: none;
  border-color: #3498db;
  background: white;
  box-shadow: 0 0 10px rgba(52, 152, 219, 0.2);
}

/* 结果面板样式 */
.result-panel {
  border: 3px solid #95a5a6;
  transition: all 0.3s ease;
}

.result-panel.has-result {
  /* 移除scale变换，避免大小变化 */
}

.encode-header {
  background: linear-gradient(45deg, #e74c3c, #c0392b) !important;
}

.decode-header {
  background: linear-gradient(45deg, #27ae60, #229954) !important;
}

.result-textarea {
  width: 100%;
  padding: 15px;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  resize: vertical;
  font-family: 'Courier New', monospace;
  box-sizing: border-box;
  rows: 8;
  min-height: 120px;
}

.encode-result {
  background: #fdf2f2;
  border: 2px solid #e74c3c;
  color: #c0392b;
}

.decode-result {
  background: #f2fdf7;
  border: 2px solid #27ae60;
  color: #1e8449;
}

/* 空状态样式 */
.empty-state {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: #7f8c8d;
  text-align: center;
}

.empty-icon {
  font-size: 4rem;
  margin-bottom: 15px;
  opacity: 0.5;
}

.empty-state p {
  font-size: 1.1rem;
  margin: 0;
}

/* 按钮样式 */
.button-group {
  display: flex;
  gap: 10px;
  margin-top: 15px;
  flex-wrap: wrap;
  flex-shrink: 0;
}

button {
  padding: 12px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.3s ease;
  flex: 1;
  min-width: 100px;
}

.encode-btn {
  background: linear-gradient(45deg, #e74c3c, #c0392b);
  color: white;
}

.encode-btn:hover:not(:disabled) {
  background: linear-gradient(45deg, #c0392b, #a93226);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(231, 76, 60, 0.4);
}

.decode-btn {
  background: linear-gradient(45deg, #27ae60, #229954);
  color: white;
}

.decode-btn:hover:not(:disabled) {
  background: linear-gradient(45deg, #229954, #1e8449);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(39, 174, 96, 0.4);
}

.clear-btn {
  background: linear-gradient(45deg, #95a5a6, #7f8c8d);
  color: white;
}

.clear-btn:hover {
  background: linear-gradient(45deg, #7f8c8d, #6c7b7d);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(149, 165, 166, 0.4);
}

button:disabled {
  background: #bdc3c7 !important;
  cursor: not-allowed;
  transform: none !important;
  box-shadow: none !important;
}

.copy-btn {
  /* 继承通用button样式 */
  padding: 12px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  transition: all 0.3s ease;
  /* 复制按钮特有样式 */
  margin: 15px auto 0 auto;
  width: calc(33.33% - 7px);
  /* 确保高度一致，不被拉伸 */
  height: 44px;
  box-sizing: border-box;
  display: block;
}

.encode-copy {
  background: linear-gradient(45deg, #e74c3c, #c0392b);
  color: white;
}

.encode-copy:hover {
  background: linear-gradient(45deg, #c0392b, #a93226);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(231, 76, 60, 0.4);
}

.decode-copy {
  background: linear-gradient(45deg, #27ae60, #229954);
  color: white;
}

.decode-copy:hover {
  background: linear-gradient(45deg, #229954, #1e8449);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(39, 174, 96, 0.4);
}

/* 响应式设计 */
@media (max-width: 768px) {
  .main-content {
    grid-template-columns: 1fr;
    height: auto;
  }
  
  .container {
    padding: 15px;
  }
  
  h1 {
    font-size: 2rem;
  }
  
  .button-group {
    flex-direction: column;
  }
  
  button {
    width: 100%;
  }
}
</style>
