<template>
  <div :class="wrapClass">
    <div ref="textRef" class="leading-relaxed break-words">
      <div  class="markdown-body" :class="{ 'markdown-body-generate': loading }" v-html="textVal" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, onMounted, onUpdated, onUnmounted } from "vue";
import MarkdownIt from "markdown-it";
import MdKatex from "@vscode/markdown-it-katex";
import "katex/dist/katex.min.css";
import MdLinkAttributes from "markdown-it-link-attributes";
import MdMermaid from "mermaid-it-markdown";
import hljs from "highlight.js";
import "highlight.js/styles/github.css"; // 引入highlight.js样式

// 组件属性定义
const props = defineProps<{
  inversion?: boolean; // 是否反转样式
  error?: boolean; // 是否显示错误状态
  text?: string; // 文本内容
  loading?: boolean; // 是否加载中
  asRawText?: boolean; // 是否显示原始文本
}>();

// 初始化markdown解析器
const mdi = new MarkdownIt({
  html: false,
  linkify: true,
  highlight(code, language) {
    const validLang = !!(language && hljs.getLanguage(language));
    if (validLang) {
      const lang = language ?? "";
      return highlightBlock(hljs.highlight(code, { language: lang }).value, lang);
    }
    return highlightBlock(hljs.highlightAuto(code).value, "");
  },
});

// 配置markdown插件
mdi
  .use(MdLinkAttributes, { attrs: { target: "_blank", rel: "noopener" } })
  .use(MdKatex)
  .use(MdMermaid);

// 样式类计算
const wrapClass = computed(() => {
  return [
    "text-wrap",
    "min-w-20px",
    "rounded-md",
    "px-3 py-2",
    props.inversion ? "bg-#d2f9d1" : "bg-#f4f6f8",
    props.inversion ? "dark:bg-#414158" : "dark:bg-#1e1e20",
    props.inversion ? "message-request" : "message-reply",
    { "text-red-500": props.error },
  ];
});

// 处理文本内容
const textVal = computed(() => {
  const value = props.text ?? "";
  if (!props.asRawText) {
    // 对数学公式进行处理，自动添加 $$ 符号
    const escapedText = escapeBrackets(escapeDollarNumber(value));
    return mdi.render(escapedText);
  }
  return value;
});

// 代码高亮块处理
function highlightBlock(str: string, lang?: string) {
  return `<pre class="code-block-wrapper"><div class="code-block-header"><span class="code-block-header__lang">${lang}</span><span class="code-block-header__copy">复制代码</span></div><code class="hljs code-block-body ${lang}">${str}</code></pre>`;
}

// 处理数学公式括号
function escapeBrackets(text: string) {
  const pattern = /(```[\s\S]*?```|`.*?`)|\\\[([\s\S]*?[^\\])\\\]|\\\((.*?)\\\)/g;
  return text.replace(pattern, (match, codeBlock, squareBracket, roundBracket) => {
    if (codeBlock) return codeBlock;
    else if (squareBracket) return `$$${squareBracket}$$`;
    else if (roundBracket) return `$${roundBracket}$`;
    return match;
  });
}

// 处理美元符号和数字
function escapeDollarNumber(text: string) {
  let escapedText = "";

  for (let i = 0; i < text.length; i += 1) {
    let char = text[i];
    const nextChar = text[i + 1] || " ";

    if (char === "$" && nextChar >= "0" && nextChar <= "9") char = "\\$";

    escapedText += char;
  }

  return escapedText;
}

// 复制功能（简化版，需要实现copyToClip函数）
const textRef = ref<HTMLElement>();

// 添加复制事件监听
function addCopyEvents() {
  if (textRef.value) {
    const copyBtn = textRef.value.querySelectorAll(".code-block-header__copy");
    copyBtn.forEach((btn) => {
      btn.addEventListener("click", () => {
        const code = btn.parentElement?.nextElementSibling?.textContent;
        if (code) {
          // 简化的复制功能
          navigator.clipboard.writeText(code).then(() => {
            btn.textContent = "复制成功";
            setTimeout(() => {
              btn.textContent = "复制代码";
            }, 1000);
          });
        }
      });
    });
  }
}

// 移除复制事件监听
function removeCopyEvents() {
  if (textRef.value) {
    const copyBtn = textRef.value.querySelectorAll(".code-block-header__copy");
    copyBtn.forEach((btn) => {
      btn.removeEventListener("click", () => {});
    });
  }
}

// 生命周期钩子
onMounted(() => {
  addCopyEvents();
});

onUpdated(() => {
  addCopyEvents();
});

onUnmounted(() => {
  removeCopyEvents();
});
</script>

<style lang="scss">
.markdown-body {
  background-color: transparent;
  font-size: 14px;

  p {
    white-space: pre-wrap;
  }

  ol {
    list-style-type: decimal;
  }

  ul {
    list-style-type: disc;
  }

  pre code,
  pre tt {
    line-height: 1.65;
  }

  .highlight pre,
  pre {
    background-color: #fff;
  }

  code.hljs {
    padding: 0;
  }

  .code-block {
    &-wrapper {
      position: relative;
      padding-top: 24px;
    }

    &-header {
      position: absolute;
      top: 5px;
      right: 0;
      width: 100%;
      padding: 0 1rem;
      display: flex;
      justify-content: flex-end;
      align-items: center;
      color: #b3b3b3;

      &__copy {
        cursor: pointer;
        margin-left: 0.5rem;
        user-select: none;

        &:hover {
          color: #65a665;
        }
      }
    }
  }

  // Mermaid
  div[id^="mermaid-container"] {
    padding: 4px;
    border-radius: 4px;
    overflow-x: auto !important;
    background-color: #fff;
    border: 1px solid #e5e5e5;
  }

  &.markdown-body-generate > dd:last-child:after,
  &.markdown-body-generate > dl:last-child:after,
  &.markdown-body-generate > dt:last-child:after,
  &.markdown-body-generate > h1:last-child:after,
  &.markdown-body-generate > h2:last-child:after,
  &.markdown-body-generate > h3:last-child:after,
  &.markdown-body-generate > h4:last-child:after,
  &.markdown-body-generate > h5:last-child:after,
  &.markdown-body-generate > h6:last-child:after,
  &.markdown-body-generate > li:last-child:after,
  &.markdown-body-generate > ol:last-child li:last-child:after,
  &.markdown-body-generate > p:last-child:after,
  &.markdown-body-generate > pre:last-child code:after,
  &.markdown-body-generate > td:last-child:after,
  &.markdown-body-generate > ul:last-child li:last-child:after {
    animation: blink 1s steps(5, start) infinite;
    color: #000;
    content: "_";
    font-weight: 700;
    margin-left: 3px;
    vertical-align: baseline;
  }

  @keyframes blink {
    to {
      visibility: hidden;
    }
  }
}

html.dark {
  .markdown-body {
    &.markdown-body-generate > dd:last-child:after,
    &.markdown-body-generate > dl:last-child:after,
    &.markdown-body-generate > dt:last-child:after,
    &.markdown-body-generate > h1:last-child:after,
    &.markdown-body-generate > h2:last-child:after,
    &.markdown-body-generate > h3:last-child:after,
    &.markdown-body-generate > h4:last-child:after,
    &.markdown-body-generate > h5:last-child:after,
    &.markdown-body-generate > h6:last-child:after,
    &.markdown-body-generate > li:last-child:after,
    &.markdown-body-generate > ol:last-child li:last-child:after,
    &.markdown-body-generate > p:last-child:after,
    &.markdown-body-generate > pre:last-child code:after,
    &.markdown-body-generate > td:last-child:after,
    &.markdown-body-generate > ul:last-child li:last-child:after {
      color: #65a665;
    }
  }

  .message-reply {
    .whitespace-pre-wrap {
      white-space: pre-wrap;
      color: var(--n-text-color);
    }
  }

  .highlight pre,
  pre {
    background-color: #000;
  }
}

@media screen and (max-width: 533px) {
  .markdown-body .code-block-wrapper {
    padding: unset;

    code {
      padding: 24px 16px 16px 16px;
    }
  }
}
</style>