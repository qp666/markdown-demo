<script lang="ts" setup>
import mpHtml from '@/components/mp-html/mp-html'

const html = reactive({
  content1: `让我为你展示一些Markdown的基本用法：

#### **计算应纳税额**
| 级数       | 全年应纳税所得额区间       | 税率（%） | 速算扣除数（元） | 计算方式                     |
|------------|----------------------------|----------|------------------|------------------------------|
| 1          | 不超过36,000元             | 3        | 0                | 36,000 × 3% = 1,080         |
| 2          | 超过36,000元至144,000元   | 10       | 2,520            | (72,200 - 36,000) × 10% = 3,620 |
| **合计**   |                            |          |                  | **应纳税额 = 1,080 + 3,620 - 2,520 = 2,200元** |

---

### 任务列表
- [x] 已完成任务
- [ ] 未完成任务
- [x] Markdown支持
- [x] 代码高亮

### Python代码示例
\`\`\`python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
\`\`\`

### 链接和图片
[Markdown指南](https://www.markdownguide.org)

功能支持
支持Markdown
 支持代码高亮
支持表格`,
})

function htmlClick() {
  console.log('11')
}
</script>

<template>
  <view class="container">
    <view class="section">
      <!-- 监听mp-html的tap事件，这样可以捕获到rich-text内部的点击 -->
      <mp-html :markdown="true" :content="html.content1" @tap="htmlClick" />
    </view>
  </view>
</template>

<style scoped>
.container {
	padding: 20px;
}
</style>
