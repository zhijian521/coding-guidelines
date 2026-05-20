# 四、CSS 规范

CSS 容易写乱，因为它的作用域是全局的。核心原则：**样式要有明确的作用范围**，改一个地方的样式不应该意外影响另一个地方。

## 4.1 BEM 命名

BEM 把类名分成三层：**Block**（独立组件）、**Element**（组件内部元素）、**Modifier**（变体）。用 `__` 连接元素，`--` 连接变体。

```css
/* Block — 独立的组件 */
.card {}

/* Element — 组件内部的子元素，用 __ 连接 */
.card__title {}
.card__body {}
.card__footer {}

/* Modifier — 组件的变体或状态，用 -- 连接 */
.card--featured { /* 特别推荐的卡片 */ }
.card__title--large { /* 大号标题 */ }

/* 状态类 — 用 is- / has- 前缀 */
.is-active {}
.has-error {}
.is-disabled {}
```

如果项目中用了 CSS Modules 或 scoped styles（Vue），BEM 就不是必须的——类名冲突已经被工具解决了。但如果写全局样式，BEM 仍然是避免命名冲突最有效的方式。

## 4.2 书写规范

| 项 | 要求 |
|---|---|
| 缩进 | 2 空格 |
| 选择器与 `{` | 保留一个空格 |
| 属性声明 | 每行一个，末尾加分号 |
| 零值 | 不加单位：`margin: 0` 而非 `margin: 0px` |
| 颜色 | 小写，能简写就简写：`#fff` 而非 `#FFFFFF` |
| `!important` | 禁止。用优先级覆盖 |
| ID 选择器 | 避免使用。ID 权重太高，后面很难覆盖 |
| SCSS 嵌套 | 不超过 3 层。嵌套太深生成的 CSS 选择器很长，性能差且难覆盖 |

```css
.card {
  display: flex;
  flex-direction: column;
  padding: 16px;
  margin: 0;
  background-color: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  transition: box-shadow .3s ease;
}

.card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, .1);
}
```

## 4.3 响应式断点

用 Mobile First 策略：先写移动端样式，再用 `min-width` 逐层覆盖更大屏幕。

```css
/* ≥ 576px：小型平板 */
@media (min-width: 576px) {}

/* ≥ 768px：平板 */
@media (min-width: 768px) {}

/* ≥ 992px：桌面端 */
@media (min-width: 992px) {}

/* ≥ 1200px：大屏 */
@media (min-width: 1200px) {}
```

> 浏览器默认字体大小是 16px。如果你在 `html` 上设置了 `font-size: 62.5%`（让 1rem = 10px），记得这只是为了方便计算，不要依赖这个 hack——部分用户会修改浏览器默认字体大小，你的页面应该能适应。

## 4.4 CSS 变量与设计令牌

项目中的颜色、间距、字号等视觉属性统一用 CSS 变量管理，定义在 `:root` 中。变量名用 `--{类别}-{属性}` 格式。

```css
:root {
  /* 颜色 */
  --color-primary: #06c;
  --color-text: #1a1a1a;
  --color-border: #e0e0e0;

  /* 间距 */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;

  /* 字体 */
  --font-size-sm: 12px;
  --font-size-base: 14px;
  --font-size-lg: 18px;
}
```

## 4.5 z-index 管理

z-index 很容易失控。约定好层级范围，全员遵守：

| 层级 | 范围 | 举例 |
|---|---|---|
| 内容层 | 0 – 99 | 普通元素、卡片、列表 |
| 浮层 | 100 – 199 | Dropdown、Tooltip、Popover |
| 遮罩层 | 200 – 299 | Modal 遮罩、Drawer 遮罩 |
| 弹窗层 | 300 – 399 | Modal、Drawer 内容 |
| 通知层 | 400 – 499 | Toast、Notification、全局 Loading |

## 4.6 动画规范

- 动画只用 `transform` 和 `opacity`——它们不触发重排，性能最好
- 避免用 `width` `height` `top` `left` 做动画
- 必须支持 `prefers-reduced-motion`：用户开启了"减少动效"时，禁用或简化动画

## 4.7 字体加载与打印

| 项 | 要求 |
|---|---|
| font-display | Web 字体必须设置 `font-display: swap`，避免 FOIT（文字不可见闪烁） |
| 打印样式 | 关键页面需提供 `@media print`，隐藏导航、侧边栏等无关元素 |
| SCSS @extend | 禁止使用 `@extend`，用 `@mixin` + `@include` 代替——extend 生成的 CSS 选择器顺序不可控 |
