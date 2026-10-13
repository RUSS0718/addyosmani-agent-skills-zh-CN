# 无障碍核对表

快速引用 WCAG 2.1 AA 的遵守。 `frontend-ui-engineering` 技术。

##  目录

- [基本检查](#essential-checks)
- [常见 HTML  图案](#common-html-patterns)
- [测试工具](#testing-tools)
- [快速引用: ARIA 现场区域](#quick-reference-aria-live-regions)
- [常见反恐怖分子](#common-anti-patterns)

## 基本检查

### 键盘导航
- [ ]  通过 Tab 键可聚焦所有交互式元素
- [ ] 焦点顺序如下: visual/logical 顺序
- [ ] 焦点可见 (outline/ring  关于重点突出的内容)
- [ ]  自定义部件有键盘支持 (输入可激活, 退出可关闭)
- [ ]  没有键盘陷阱 (用户可以总是从一个组件中选择标签)
- [ ] 页面顶部跳转到内容链接 - 可见 (起码) 键盘焦点
- [ ] 打开 Modals 陷阱时聚焦, 返回焦点于关闭

### 屏幕阅读器
- [ ]  所有图像都有 `alt` 文本 (或 `alt=""` 用于装饰图像)
- [ ]  所有窗体输入都有相关的标签 (`<label>` 或 `aria-label`)
- [ ] 按钮和链接有描述文本 ( 不是"点击这里")
- [ ]  只有图标的按钮有 `aria-label`
- [ ] 页面有一个 `<h1>` 且标题不会跳过关卡
- [ ] 已宣布动态内容更改 (`aria-live` 区域)
- [ ] 表格有 `<th>`  有范围的标题

### 视觉
- [ ] 文本对比 \ 4.5:1 (常规文本) 或                                                              ( 大文本, 18px+ 键)
- [ ] UI 3:1 与背景对比
- [ ] 颜色不是传递信息的唯一方法
- [ ] 文本可调整到200%而不中断布局
- [ ]  没有内容闪烁超过每秒3次

###  表单
- [ ]  每个输入都有明显的标签
- [ ] 显示的所需字段 ( 不单靠颜色)
- [ ] 与字段相关的特定错误信息
- [ ] 以多颜色可见的错误状态 (图标、文本、边框)
- [ ] 表格提交错误摘要和可聚焦
- [ ] 已知字段使用自动完成 (举例来说, `type="email" autocomplete="email"`)

### 内容
- [ ] 所申报的语言 (`<html lang="en">`)
- [ ] 页面有一个描述 `<title>`
- [ ] 链接区别于周围的文本 ( 不单靠颜色)
- [ ] 移动式44x44px的触摸目标
- [ ] 有意义的空状态 ( 不设空白屏幕)

## 常见 HTML  图案

### 按钮对链接

```html
<!-- Use <button> for actions -->
<button onClick={handleDelete}>Delete Task</button>

<!-- Use <a> for navigation -->
<a href="/tasks/123">View Task</a>

<!-- NEVER use div/span as buttons -->
<div onClick={handleDelete}>Delete</div>  <!-- BAD -->
```

### 窗体标签

```html
<!-- Explicit label association -->
<label htmlFor="email">Email address</label>
<input id="email" type="email" required />

<!-- Implicit wrapping -->
<label>
  Email address
  <input type="email" required />
</label>

<!-- Hidden label (visible label preferred) -->
<input type="search" aria-label="Search tasks" />
```

### ARIA 角色

```html
<!-- Navigation -->
<nav aria-label="Main navigation">...</nav>
<nav aria-label="Footer links">...</nav>

<!-- Status messages -->
<div role="status" aria-live="polite">Task saved</div>

<!-- Alert messages -->
<div role="alert">Error: Title is required</div>

<!-- Modal dialogs -->
<dialog aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirm Delete</h2>
  ...
</dialog>

<!-- Loading states -->
<div aria-busy="true" aria-label="Loading tasks">
  <Spinner />
</div>
```

###  可访问列表

```html
<ul role="list" aria-label="Tasks">
  <li>
    <input type="checkbox" id="task-1" aria-label="Complete: Buy groceries" />
    <label htmlFor="task-1">Buy groceries</label>
  </li>
</ul>
```

## 测试工具

```bash
# Automated audit
npx axe-core          # Programmatic accessibility testing
npx pa11y             # CLI accessibility checker

# In browser
# Chrome DevTools → Lighthouse → Accessibility
# Chrome DevTools → Elements → Accessibility tree

# Screen reader testing
# macOS: VoiceOver (Cmd + F5)
# Windows: NVDA (free) or JAWS
# Linux: Orca
```

## 快速引用: ARIA 现场区域

| 数值 | 行为 | 用于 |
|-------|----------|---------|
| `aria-live="polite"` |  下次暂停时宣布 | 状态更新, 保存确认 |
| `aria-live="assertive"` | 即时宣布 | 错误、 时间敏感提醒 |
| `role="status"` | 与 `polite` | 状态信息 |
| `role="alert"` | 与 `assertive` | 错误消息 |

## 常见反恐怖分子

| 反治疗者 | 问题 | 修补 |
|---|---|---|
| `div`  作为按钮 |  不突出重点, 没有键盘支持 | 使用( U) `<button>` |
| 失踪 `alt` 文本 | 屏幕阅读器无法显示的图像 | 添加说明 `alt` |
| 纯色状态 | 看不见色盲用户 | 添加图标、文本或图案 |
| 自动播放媒体 | 迷惑人心,无法阻止 | 添加控件, 不自动播放 |
|  自定义没有 ARIA 的下拉 | 无法使用 keyboard/screen 阅读器 | 使用本地语言 `<select>` 或适当的 ARIA 列表框 |
|  删除焦点大纲 | 用户看不到他们在哪里 | 样式轮廓,不要去掉 |
| 空无 links/buttons | "Link"没有描述就宣布了 | 添加文本或 `aria-label` |
| `tabindex > 0` | 打破自然制表符顺序 | 使用( U) `tabindex="0"` 或 `-1` 仅限 |
