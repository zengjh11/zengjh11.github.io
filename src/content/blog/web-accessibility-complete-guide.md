---
title: "Web 可访问性完全指南"
description: "学习如何构建对所有用户友好的网页，包括使用辅助技术的人群。"
date: 2025-02-03
tags: ["可访问性", "a11y", "HTML", "前端开发", "无障碍"]
author: "博客作者"
---

Web 可访问性（Web Accessibility，简称 a11y）是指让网站能够被尽可能多的人使用，包括使用辅助技术的用户，如屏幕阅读器、键盘导航、高对比度模式等。本文将介绍可访问性的核心概念和最佳实践，帮助你构建更包容的网页。

## 为什么可访问性很重要

### 1. 包容性设计

可访问性不仅仅是为残障人士设计，它让所有用户受益：

- **视觉障碍**：色盲、低视力、完全失明
- **听觉障碍**：听力损失或完全失聪
- **运动障碍**：无法使用鼠标，只能使用键盘
- **认知障碍**：学习困难、注意力问题
- **情境限制**：强光下的低对比度、嘈杂环境中的音频内容

### 2. 法律和合规要求

许多国家和地区都有关于数字可访问性的法律要求：

- **美国**：ADA（Americans with Disabilities Act）
- **欧盟**：European Accessibility Act
- **中国**：《无障碍环境建设法》

### 3. SEO 和用户体验

可访问性实践通常也能改善 SEO：

- 语义化 HTML 结构对搜索引擎更友好
- 图片的 alt 文本帮助搜索引擎理解内容
- 良好的对比度提高所有用户的可读性

## 可访问性的四个原则（POUR）

### 1. 可感知（Perceivable）

信息和用户界面组件必须以用户可以感知的方式呈现：

```html
<!-- 不好的例子：没有 alt 的图片 -->
<img src="chart.png" />

<!-- 好的例子：提供替代文本 -->
<img src="chart.png" alt="2024年销售增长图，显示Q1到Q4增长25%" />
```

### 2. 可操作（Operable）

用户界面组件和导航必须是可操作的：

```html
<!-- 不好的例子：div 模拟按钮，无法键盘访问 -->
<div class="button" onclick="submit()">提交</div>

<!-- 好的例子：使用正确的按钮元素 -->
<button type="submit" onclick="submit()">提交</button>
```

### 3. 可理解（Understandable）

信息和用户界面的操作必须是可理解的：

```html
<!-- 不好的例子：模糊的标签 -->
<input type="text" placeholder="输入" />

<!-- 好的例子：清晰的标签和说明 -->
<label for="email">电子邮箱</label>
<input
  type="email"
  id="email"
  name="email"
  placeholder="example@email.com"
  aria-describedby="email-help"
/>
<p id="email-help">我们将使用此邮箱发送验证链接</p>
```

### 4. 健壮性（Robust）

内容必须足够健壮，能够被各种用户代理（包括辅助技术）可靠地解析：

```html
<!-- 使用语义化 HTML 结构 -->
<header>
  <nav aria-label="主导航">
    <ul>
      <li><a href="/">首页</a></li>
      <li><a href="/about">关于</a></li>
    </ul>
  </nav>
</header>

<main>
  <article>
    <h1>文章标题</h1>
    <p>文章内容...</p>
  </article>
</main>

<footer>
  <p>&copy; 2025 版权所有</p>
</footer>
```

## 核心可访问性实践

### 1. 语义化 HTML

使用正确的 HTML 元素传达含义：

```html
<!-- 不好的例子 -->
<div class="nav">
  <div class="nav-item" onclick="goHome()">首页</div>
</div>

<!-- 好的例子 -->
<nav>
  <a href="/">首页</a>
</nav>
```

### 2. 图片的替代文本

为所有图片提供有意义的 alt 属性：

```html
<!-- 装饰性图片 -->
<img src="divider.png" alt="" />

<!-- 信息性图片 -->
<img src="logo.png" alt="公司Logo" />

<!-- 复杂图表 -->
<img src="sales-chart.png" alt="2024年销售增长图" />
<figure>
  <figcaption>详细数据：Q1: 100万, Q2: 120万, Q3: 150万, Q4: 180万</figcaption>
</figure>
```

### 3. 表单的可访问性

确保表单对所有用户都可用：

```html
<form>
  <fieldset>
    <legend>个人信息</legend>

    <div class="form-group">
      <label for="username">用户名 *</label>
      <input
        type="text"
        id="username"
        name="username"
        required
        aria-required="true"
        aria-describedby="username-error"
      />
      <span id="username-error" class="error" role="alert"></span>
    </div>

    <div class="form-group">
      <label for="country">国家</label>
      <select id="country" name="country">
        <option value="">请选择</option>
        <option value="cn">中国</option>
        <option value="us">美国</option>
      </select>
    </div>

    <div class="form-group">
      <fieldset>
        <legend>兴趣爱好</legend>
        <input type="checkbox" id="sports" name="interests" value="sports" />
        <label for="sports">运动</label>

        <input type="checkbox" id="music" name="interests" value="music" />
        <label for="music">音乐</label>
      </fieldset>
    </div>
  </fieldset>

  <button type="submit">提交</button>
</form>
```

### 4. 键盘导航

确保所有功能都可以通过键盘访问：

```css
/* 可见的焦点指示器 */
:focus {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}

/* 键盘焦点的特殊样式 */
:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}

/* 不要完全移除焦点样式 */
button:focus:not(:focus-visible) {
  outline: none;
}
```

```javascript
// 跳过链接实现
document.querySelector(".skip-link").addEventListener("click", (e) => {
  e.preventDefault();
  const mainContent = document.querySelector("#main-content");
  mainContent.focus();
  mainContent.scrollIntoView();
});
```

### 5. ARIA 属性

当 HTML 语义不足时使用 ARIA：

```html
<!-- 自定义下拉菜单 -->
<div
  class="dropdown"
  role="combobox"
  aria-expanded="false"
  aria-haspopup="listbox"
>
  <button aria-controls="dropdown-list" aria-labelledby="dropdown-label">
    选择选项
  </button>
  <ul id="dropdown-list" role="listbox" aria-hidden="true">
    <li role="option" aria-selected="false">选项 1</li>
    <li role="option" aria-selected="false">选项 2</li>
  </ul>
</div>

<!-- 进度条 -->
<div
  role="progressbar"
  aria-valuenow="75"
  aria-valuemin="0"
  aria-valuemax="100"
  aria-label="加载进度"
>
  75%
</div>

<!-- 实时区域 -->
<div role="status" aria-live="polite" aria-atomic="true">
  <p>3 条新消息</p>
</div>
```

### 6. 颜色对比度

确保文本与背景有足够的对比度：

```css
/* 良好的对比度 - 4.5:1 以上 */
.text-primary {
  color: #333333; /* 深灰 */
  background-color: #ffffff; /* 白 */
}

/* 大文本可以使用稍低的对比度 - 3:1 */
.text-large {
  color: #666666;
  background-color: #ffffff;
  font-size: 18px;
  font-weight: bold;
}

/* 避免仅靠颜色传达信息 */
.error {
  color: #d32f2f;
  border-left: 4px solid #d32f2f; /* 额外的视觉提示 */
  padding-left: 12px;
}

.error::before {
  content: "⚠️ "; /* 图标提示 */
}
```

### 7. 动态内容的可访问性

处理动态更新和模态框：

```javascript
// 模态框的焦点管理
function openModal() {
  const modal = document.getElementById("modal");
  const firstFocusable = modal.querySelector(
    "button, [href], input, select, textarea",
  );
  const previousFocus = document.activeElement;

  modal.style.display = "block";
  modal.setAttribute("aria-hidden", "false");
  firstFocusable.focus();

  // 聚焦陷阱
  modal.addEventListener("keydown", (e) => {
    if (e.key === "Escape") {
      closeModal();
      previousFocus.focus();
    }
  });
}

// 页面标题更新
document.title = "新页面标题 - 我的网站";

// 通知屏幕阅读器用户
function announce(message) {
  const announcement = document.createElement("div");
  announcement.setAttribute("role", "status");
  announcement.setAttribute("aria-live", "polite");
  announcement.className = "sr-only";
  announcement.textContent = message;

  document.body.appendChild(announcement);
  setTimeout(() => announcement.remove(), 1000);
}
```

## 可访问性测试工具

### 1. 自动化测试工具

```bash
# Lighthouse (Chrome DevTools 内置)
# axe-core
npm install @axe-core/cli

# Pa11y
npm install -g pa11y
pa11y https://example.com
```

### 2. 浏览器扩展

- **axe DevTools**：Chrome/Firefox 扩展
- **WAVE**：Web Accessibility Evaluation Tool
- **Lighthouse**：Chrome DevTools 内置

### 3. 手动测试清单

- [ ] 使用 Tab 键导航整个页面
- [ ] 检查所有交互元素都有焦点样式
- [ ] 使用屏幕阅读器（NVDA、JAWS、VoiceOver）测试
- [ ] 验证颜色对比度符合 WCAG 标准
- [ ] 测试放大到 200% 时的布局
- [ ] 禁用 CSS 检查页面结构

### 4. CSS 辅助工具

```css
/* 屏幕阅读器专用类 */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

/* 减少动画偏好 */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}

/* 高对比度模式 */
@media (prefers-contrast: high) {
  .button {
    border: 2px solid currentColor;
  }
}
```

## 总结

Web 可访问性不仅是道德责任，也是法律要求和商业需求。通过遵循 POUR 原则和最佳实践，你可以创建对所有用户都友好的网站。

记住可访问性的核心原则：

1. **使用语义化 HTML** - 这是最好的基础
2. **提供替代内容** - 为图片、视频、音频提供替代方案
3. **确保键盘可访问** - 所有功能都可以通过键盘操作
4. **设计良好的对比度** - 确保内容易于阅读
5. **测试验证** - 使用自动化工具和手动测试
6. **持续改进** - 可访问性是持续的过程，不是一次性的任务

通过将这些实践融入日常开发流程，你将构建更加包容和健壮的 Web 应用。

## 参考资源

- [WCAG 2.1 指南](https://www.w3.org/WAI/WCAG21/quickref/)
- [MDN Web Accessibility](https://developer.mozilla.org/zh-CN/docs/Web/Accessibility)
- [WebAIM](https://webaim.org/)
- [A11y Project](https://www.a11yproject.com/)
