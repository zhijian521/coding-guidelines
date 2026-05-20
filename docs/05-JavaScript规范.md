# 五、JavaScript 规范

JS 是最容易写出坏代码的语言之一——它的灵活性既是优点也是陷阱。以下规则围绕**可读性**和**可维护性**展开。

## 5.1 变量声明

永远用 `const` 和 `let`，忘记 `var` 的存在。`var` 有变量提升和作用域问题，是早期 JS 的设计缺陷。用字面量创建对象和数组，别用 `new Object()` 或 `new Array()`——字面量更短，而且不会有 `new Array(3)` 产生的歧义。

```javascript
// const 声明常量，let 声明可变变量，禁止 var
const name = 'Alice';
let count = 0;

// 字面量创建对象和数组
const user = { name: 'Alice', age: 25 };
const items = [1, 2, 3];

// 解构赋值 — 少写很多 .xxx
const { name, age } = user;
const [first, second] = items;

// 模板字符串 — 告别 + 号拼接
const greeting = `Hello, ${name}!`;
```

## 5.2 函数规范

| 项 | 要求 | 原因 |
|---|---|---|
| 行数 | 原则上不超过 80 行 | 超过说明职责太多，应该拆分 |
| 参数 | 不超过 4 个，超过用 options 对象 | 参数太多调用时容易搞错顺序 |
| 箭头函数 | 回调优先用箭头函数 | `this` 指向不会变，少写 bind |
| 早期返回 | 用 early return 减少嵌套 | 条件判断放前面，主线逻辑保持扁平 |
| 纯函数优先 | 尽量不依赖外部状态 | 输入一样输出就一样，容易测试和推理 |

**不推荐 — 嵌套过深：**
```javascript
function processOrder(order) {
  if (order) {
    if (order.status === 'pending') {
      if (order.amount > 0) {
        if (order.items.length > 0) {
          return validate(order);
        }
      }
    }
  }
}
```

**推荐 — 早期返回：**
```javascript
function processOrder(order) {
  if (!order) return null;
  if (order.status !== 'pending') return null;
  if (order.amount <= 0) return null;
  if (!order.items.length) return null;
  return validate(order);
}
```

左边之所以不好，不是因为它写错了，而是读起来累——每次看都要把四层 if 在脑子里展开。右边把所有边界条件先排除，最后只剩一条清晰的主线。

## 5.3 异步处理

`async/await` 是写异步代码的首选。它比 `.then()` 链更容易读，错误处理也更自然。多个互不依赖的请求用 `Promise.all` 并行发出。

```javascript
// async/await + try/catch
async function fetchUserData(userId) {
  try {
    const user = await api.get(`/users/${userId}`);
    const posts = await api.get(`/users/${userId}/posts`);
    return { user: user.data, posts: posts.data };
  } catch (error) {
    console.error('获取失败:', error);
    throw error;
  }
}

// 互不依赖的请求用 Promise.all 并行
const [users, products] = await Promise.all([
  fetchUsers(),
  fetchProducts(),
]);
```

## 5.4 数组与对象操作

用 `map` `filter` `reduce` 替代传统的 for 循环。不是性能问题——现代引擎对这些高阶函数优化得很好——而是它们**表达意图更清晰**。

```javascript
// 扩展运算符 — 浅拷贝、合并的利器
const copy = [...original];
const merged = { ...defaults, ...options };

// 高阶函数 — 一眼看出在做什么
const activeUsers = users.filter(u => u.isActive);
const names = users.map(u => u.name);
const total = orders.reduce((s, o) => s + o.amount, 0);

// 严格相等 === 而非 ==
if (value === null) {}
if (items.length === 0) {}
```

## 5.5 模块导入顺序

import 语句按以下顺序分组，组之间空一行：第三方库 → 路径别名（`@/`）→ 相对路径。

```javascript
// 1. 第三方库
import React from 'react';
import { debounce } from 'lodash-es';

// 2. 路径别名
import { UserCard } from '@/components';
import { userApi } from '@/api';

// 3. 相对路径
import { formatDate } from './utils';
import styles from './styles.module.css';
```

## 5.6 错误处理

任何可能失败的异步操作必须有 try/catch。catch 中至少要做两件事：记录错误（方便排查）和提供用户友好的提示。

```javascript
async function submitForm(data) {
  try {
    await api.post('/submit', data);
    showSuccess('提交成功');
  } catch (error) {
    console.error('提交失败', error);
    showError('提交失败，请稍后重试');
  }
}
```

## 5.7 定时器与事件监听清理

`setInterval` / `setTimeout` 必须在组件卸载或不再需要时清除，否则会造成内存泄漏和意外行为。同样，`addEventListener` 必须有对应的 `removeEventListener`。Vue 中用 `onUnmounted`，React 中用 `useEffect` 的 return 清理函数。

## 5.8 浏览器存储与 URL 处理

| 项 | 要求 |
|---|---|
| localStorage key | 使用命名空间前缀避免冲突：`myapp:token`、`myapp:theme` |
| 敏感数据 | 禁止在 localStorage/sessionStorage 中存储密码、Token 等敏感信息 |
| URL 参数 | 传递到 URL 的字符串必须用 `encodeURIComponent` 编码 |
| URL 构建 | 用 `URL` 或 `URLSearchParams` API 拼接参数，不要手动拼字符串 |

> `parseInt()` 必须带第二个参数指定基数，否则 `parseInt('08')` 在老引擎里会返回 0。条件判断和循环嵌套最多 3 层。`console.log` 提交前删干净。禁止 `new String()` `new Number()` `new Boolean()`。
