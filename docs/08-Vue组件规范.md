# 八、Vue 组件规范

Vue 的灵活性是把双刃剑——同一个功能可以用好几种方式实现。以下规则不是为了限制你，而是让团队成员之间写出来的组件结构一致，互相能快速看懂。

## 8.1 命名规则

组件名必须多个单词，因为 HTML 标准元素全是单单词（`<input>`、`<nav>`），用多单词避免和未来的 HTML 元素冲突。

| 项 | 规则 | 示例 |
|---|---|---|
| 组件名 | 多单词，PascalCase | `UserProfile` |
| 基础组件 | `Base` / `App` 前缀 | `BaseButton` |
| 单例组件 | `The` 前缀 | `TheHeader` |
| 紧密耦合 | 父组件名前缀 | `TodoList` → `TodoListItem` |
| 模板中使用 | kebab-case | `<user-profile />` |

## 8.2 单文件组件结构

一个标准的 Vue SFC 按 `<template>` → `<script setup>` → `<style scoped>` 的顺序排列。

```vue
<template>
  <div class="user-profile">
    <h2>{{ user.name }}</h2>
    <base-button @click="handleEdit">编辑</base-button>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import type { User } from '@/types';

const props = defineProps<{
  user: User;
  editable?: boolean;
}>();

const emit = defineEmits<{
  (e: 'update', user: User): void;
}>();

const isEditing = ref(false);

const displayName = computed(() =>
  props.user.name || '未知用户'
);

function handleEdit() {
  emit('update', props.user);
}
</script>

<style scoped>
.user-profile {
  padding: 16px;
}
</style>
```

## 8.3 规范要点

| 项 | 要求 | 原因 |
|---|---|---|
| 语法 | 推荐 `<script setup>` | 比 Options API 更简洁，类型推导更好 |
| Props | 必须定义类型、默认值 | 让使用组件的人知道传什么参数 |
| v-for | 必须设置唯一 `key` | Vue 用 key 追踪节点，没有 key 会导致渲染错乱 |
| v-if / v-show | 频繁切换用 `v-show` | v-if 每次切换都销毁重建，v-show 只是 display 切换 |
| v-if 与 v-for | 禁止同一元素同时使用 | v-for 优先级高于 v-if，会导致不必要的循环 |
| 样式作用域 | `scoped` 或 CSS Modules | 防止样式污染全局 |
| 路由 | 动态 `import()` 懒加载 | 首屏只加载当前页面代码，减少白屏时间 |
| 模板表达式 | 保持简单 | 复杂逻辑放 computed，模板只负责展示 |

## 8.4 Composable 与高级特性

| 项 | 要求 |
|---|---|
| Composable 命名 | `use` 前缀，文件名与函数名一致：`useAuth.ts` → `useAuth()` |
| Composable 返回值 | 返回 `{ data, loading, error, execute }` 结构，调用方按需解构 |
| 插槽命名 | kebab-case：`<template #header-actions>` |
| provide/inject | Key 用 Symbol 或常量避免冲突；inject 必须提供默认值 |
| watch vs watchEffect | 需要对比新旧值时用 `watch`；只需追踪依赖自动执行时用 `watchEffect` |
| defineExpose | 只在确实需要父组件调用子组件方法时使用，尽量不暴露内部状态 |
| Teleport | 用于 Modal、Toast 等需要脱离组件层级渲染的场景，目标元素确保存在 |
