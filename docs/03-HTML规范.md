# 三、HTML 规范

HTML 是页面的骨架。写 HTML 时记住两点：**语义化**（让标签本身描述内容的含义）和**可访问性**（让屏幕阅读器和搜索引擎能正确理解页面结构）。

## 3.1 基本模板

每个 HTML 页面从这个模板开始。注意 `lang` 属性和 viewport 设置——这两个缺了会影响可访问性和移动端显示。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="页面描述">
  <title>页面标题</title>
</head>
<body>
</body>
</html>
```

## 3.2 规范要点

| 项 | 要求 | 原因 |
|---|---|---|
| DOCTYPE | `<!DOCTYPE html>` | 告诉浏览器用标准模式渲染，不用这个会触发怪异模式 |
| 编码 | UTF-8 | 统一编码，避免中文乱码 |
| 语义化 | `<header>` `<nav>` `<main>` `<footer>` | 不要全用 `<div>`。搜索引擎和读屏软件依赖标签理解页面结构 |
| 属性引号 | 双引号 | 统一风格，避免属性值含空格时解析错误 |
| 标签闭合 | 不要省略可选关闭标签 | `<li>` `<p>` 等标签虽然可以省略闭合，但写了更容易阅读和 diff |
| 缩进 | 2 空格 | 嵌套结构一目了然 |
| 内联样式 | 禁止 | 样式应通过 class 控制，内联样式难以维护和覆盖 |
| `target="_blank"` | 加 `rel="noopener noreferrer"` | 防止新页面通过 `window.opener` 操作原页面，是安全漏洞 |
| 图片 | 必须写 `alt` 属性 | 图片加载失败或读屏时提供文字说明 |

## 3.3 属性书写顺序

属性按以下顺序写，这样一眼就能找到关键属性：

1. `class` — 样式相关，最常修改
2. `id` — 尽量少用
3. `name`、`data-*`
4. `src`、`type`、`href`、`value`
5. `placeholder`、`title`、`alt`
6. `aria-*`、`role`
7. `required`、`readonly`、`disabled`

## 3.4 表单元素

| 项 | 要求 | 原因 |
|---|---|---|
| label 关联 | 每个 input 必须有 `<label>` 或 `aria-label` | 点击 label 自动聚焦 input，读屏软件读出字段名 |
| autocomplete | 使用标准值如 `email` `tel` `name` | 浏览器自动填充，减少用户输入 |
| input type | 用语义化 type：`email` `tel` `number` `date` | 移动端弹出正确键盘，自带格式校验 |
| required | 必填字段加 `required` 属性 + 视觉标识 | 浏览器原生校验 + 无障碍提示 |

## 3.5 图片规范

```html
<!-- 响应式图片：不同屏幕加载不同尺寸 -->
<img
  srcset="img-480.jpg 480w, img-800.jpg 800w, img-1200.jpg 1200w"
  sizes="(max-width: 600px) 480px, (max-width: 1000px) 800px, 1200px"
  src="img-800.jpg"
  alt="产品图片"
  loading="lazy"
/>

<!-- 现代格式优先，降级兼容 -->
<picture>
  <source srcset="img.webp" type="image/webp">
  <source srcset="img.avif" type="image/avif">
  <img src="img.jpg" alt="">
</picture>
```

## 3.6 资源预加载

关键资源（首屏字体、Hero 图片、核心 CSS）用 preload 提前加载；下一页可能用到的资源用 prefetch 空闲时加载；第三方域名用 dns-prefetch 或 preconnect 提前解析 DNS。

```html
<!-- 关键字体预加载 -->
<link rel="preload" href="/fonts/main.woff2" as="font" crossorigin>

<!-- 下一页预取 -->
<link rel="prefetch" href="/page-2.js">

<!-- 第三方域名预连接 -->
<link rel="preconnect" href="https://api.example.com">
```

## 3.7 iframe 与嵌入内容

| 项 | 要求 |
|---|---|
| 安全 | 必须加 `sandbox` 属性，限制 iframe 权限 |
| 加载 | 加 `loading="lazy"` 延迟加载 |
| 无障碍 | 加 `title` 属性描述 iframe 内容 |
| 尺寸 | 明确设置宽高，避免布局偏移 (CLS) |
