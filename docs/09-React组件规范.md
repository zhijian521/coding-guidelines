# 九、React 组件规范

React 的函数组件 + Hooks 模式已经很成熟了。Class 组件是历史遗留，新代码全部用函数组件。关键是控制好重渲染——React 默认父组件更新时子组件也会更新，合理使用 `useMemo` 和 `useCallback` 能省掉很多不必要的渲染。

## 9.1 函数组件

Props 接口写在组件上方，一眼能看到组件需要什么数据。事件处理函数以 `handle` 开头，方便在 JSX 里和 `onClick` 等原生事件区分。

```tsx
import { useState, useCallback, useMemo } from 'react';
import type { FC } from 'react';

interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void;
}

export const UserCard: FC<UserCardProps> = ({ user, onEdit }) => {
  const [isExpanded, setExpanded] = useState(false);

  const handleEdit = useCallback(() => {
    onEdit?.(user);
  }, [onEdit, user]);

  const displayName = useMemo(() =>
    user.nickname || user.name,
  [user]);

  return (
    <div>
      <h3>{displayName}</h3>
      <button onClick={handleEdit}>编辑</button>
    </div>
  );
};

UserCard.displayName = 'UserCard';
```

## 9.2 规范要点

| 项 | 要求 | 原因 |
|---|---|---|
| 组件类型 | 函数组件 + Hooks | Class 组件语法冗余，Hooks 更灵活且逻辑复用更方便 |
| Props | TypeScript interface 定义 | 类型安全，编辑器自动补全 |
| 自定义 Hook | `use` 前缀 | React 的规则：Hook 必须以 use 开头 |
| 事件处理 | `handle` 前缀 | 区别于原生事件属性名 |
| 组件大小 | 不超过 300 行 | 超过说明职责太多，应该拆分 |
| 内联函数 | 避免在 JSX 中直接写 | 每次渲染都创建新函数引用，导致子组件不必要的重渲染 |
| displayName | 必须设置 | React DevTools 中显示的名字，方便调试 |

## 9.3 useEffect 与性能细节

| 项 | 要求 |
|---|---|
| useEffect 清理 | 订阅、定时器、事件监听必须在 return 中清理，避免内存泄漏 |
| Context 使用 | 只有真正需要跨多层级共享的数据才放 Context（主题、语言、认证），不要替代 Props |
| 列表 key | 用唯一 ID（数据库主键），禁止用 `index`——数据顺序变化时会导致渲染错乱 |
| forwardRef | 需要暴露 DOM 节点给父组件时使用，优先用 Props 回调替代 ref 操作 |
| lazy + Suspense | 非首屏组件用 `lazy()` 动态导入，配合 `<Suspense>` 提供加载中的 fallback UI |
