<script lang="ts" setup>
import mpHtml from '@/components/mp-html/mp-html'

// 导航到markdown测试页面
function goToMarkdownTest() {
  uni.navigateTo({
    url: '/pages/markdown-test'
  })
}

const html = reactive({
  content1: `\`\`\`python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
\`\`\``,
  content2: `\`\`\`mermaid
graph TD
    A[开始] --> B{是否满足条件?}
    B -->|是| C[执行操作]
    B -->|否| D[跳过操作]
    C --> E[结束]
    D --> E
\`\`\``,
  content3: `\`\`\`mermaid
sequenceDiagram
    participant A as 用户
    participant B as 系统
    participant C as 数据库
    
    A->>B: 发送请求
    B->>C: 查询数据
    C-->>B: 返回结果
    B-->>A: 响应数据
\`\`\``,
  content4: `\`\`\`mermaid
pie title 项目进度
    "已完成" : 60
    "进行中" : 25
    "未开始" : 15
\`\`\``,
})
</script>

<template>
  <view class="container">
    <!-- 导航按钮 -->
    <view class="nav-section">
      <button @click="goToMarkdownTest" class="nav-btn">测试 Markdown 组件</button>
    </view>
    
    <view class="section">
      <text class="title">
        Python代码高亮
      </text>
      <mp-html :markdown="true" :content="html.content1" />
    </view>

    <view class="section">
      <text class="title">
        Mermaid流程图
      </text>
      <mp-html :markdown="true" :content="html.content2" />
    </view>

    <view class="section">
      <text class="title">
        Mermaid时序图
      </text>
      <mp-html :markdown="true" :content="html.content3" />
    </view>

    <view class="section">
      <text class="title">
        Mermaid饼图
      </text>
      <mp-html :markdown="true" :content="html.content4" />
    </view>
  </view>
</template>

<style scoped>
.container {
	padding: 20px;
}

.nav-section {
	margin-bottom: 20px;
	text-align: center;
}

.nav-btn {
	padding: 12px 24px;
	background-color: #007aff;
	color: white;
	border: none;
	border-radius: 6px;
	font-size: 16px;
	cursor: pointer;
	transition: background-color 0.3s;
}

.nav-btn:hover {
	background-color: #0056cc;
}

.section {
	margin-bottom: 30px;
	border: 1px solid #e0e0e0;
	border-radius: 8px;
	padding: 15px;
}

.title {
	font-size: 18px;
	font-weight: bold;
	color: #333;
	margin-bottom: 10px;
	display: block;
}
</style>
