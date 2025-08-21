<template>
  <view class="container">
    <view class="header">
      <text class="title">Markdown 展示测试</text>
    </view>
    
    <!-- 使用Message组件展示markdown内容 -->
    <MessageText 
      :text="markdownContent" 
      :loading="false" 
      :inversion="false" 
      :as-raw-text="false"
    />
    
    <!-- 测试按钮 -->
    <view class="button-group">
      <button @click="changeContent" class="test-btn">切换内容</button>
      <button @click="toggleLoading" class="test-btn">切换加载状态</button>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import MessageText from '@/components/Message/Text.vue'

// 测试用的markdown内容
const markdownContent = ref(`# Markdown 展示测试

这是一个 **粗体文本** 和 *斜体文本* 的示例。

## 代码块测试

\`\`\`mermaid
graph TD
    A[开始] --> B{是否满足条件?}
    B -->|是| C[执行操作]
    B -->|否| D[跳过操作]
    C --> E[结束]
    D --> E
\`\`\`

\`\`\`mermaid
sequenceDiagram
    participant A as 用户
    participant B as 系统
    participant C as 数据库
    
    A->>B: 发送请求
    B->>C: 查询数据
    C-->>B: 返回结果
    B-->>A: 响应数据
\`\`\`

\`\`\`mermaid
pie
    title 饼图示例
    "A" : 30
    "B" : 20
    "C" : 50
\`\`\`

## 列表测试

### 无序列表
- 项目 1
- 项目 2
  - 子项目 2.1
  - 子项目 2.2
- 项目 3

### 有序列表
1. 第一项
2. 第二项
3. 第三项

## 链接测试

这是一个 [链接示例](https://www.example.com)

## 数学公式测试

行内公式：$E = mc^2$

块级公式：
$$\\int_{-\\infty}^{\\infty} e^{-x^2} dx = \\sqrt{\\pi}$$

## 引用测试

> 这是一个引用块
> 可以包含多行内容

## 表格测试

| 列1 | 列2 | 列3 |
|-----|-----|-----|
| 数据1 | 数据2 | 数据3 |
| 数据4 | 数据5 | 数据6 |
`)

const loading = ref(false)
const contentIndex = ref(0)

// 多个测试内容
const testContents = [
  markdownContent.value,
  `# 简单测试

这是一个简单的测试内容。

\`\`\`python
print("Hello, Python!")
for i in range(5):
    print(f"数字: {i}")
\`\`\`

**这是粗体文本**`,
  `# AI 提示测试

当我在项目中写到ai时，请出ai提示。

这里包含了 **AI** 相关的内容，应该会触发特殊处理。

## 代码示例

\`\`\`typescript
// AI 相关代码
interface AIResponse {
  content: string;
  reasoning?: string;
}
\`\`\`
`
]

// 切换内容
function changeContent() {
  contentIndex.value = (contentIndex.value + 1) % testContents.length
  markdownContent.value = testContents[contentIndex.value]
}

// 切换加载状态
function toggleLoading() {
  loading.value = !loading.value
}
</script>

<style lang="scss" scoped>
.container {
  padding: 20px;
  min-height: 100vh;
  background-color: #f5f5f5;
}

.header {
  text-align: center;
  margin-bottom: 20px;
  
  .title {
    font-size: 24px;
    font-weight: bold;
    color: #333;
  }
}

.button-group {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 20px;
  
  .test-btn {
    padding: 10px 20px;
    background-color: #007aff;
    color: white;
    border: none;
    border-radius: 5px;
    font-size: 14px;
    cursor: pointer;
    
    &:hover {
      background-color: #0056cc;
    }
  }
}
</style>