# 六、TypeScript 规范

TypeScript 的核心价值不是"写类型标注"，而是**让编译器在你点下保存按钮时就告诉你哪里可能出错**，而不是等用户撞上了才发现。开严格模式，不要偷懒用 `any`。

## 6.1 严格模式

新项目 `tsconfig.json` 必须开启 `strict: true`。这相当于一次性打开了所有严格检查开关。

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## 6.2 类型定义

用 `interface` 定义对象形状，用 `type` 定义联合类型和工具类型。

```typescript
// interface — 定义对象的结构（可被 extend）
interface User {
  id: string;
  name: string;
  email: string;
  age?: number;          // ? 表示可选
  role: UserRole;
}

// type — 联合类型、交叉类型、工具类型
type UserRole = 'admin' | 'editor' | 'viewer';
type ApiResponse<T> = {
  code: number;
  data: T;
  message: string;
};

// enum — 一组有名字的常量
enum OrderStatus {
  Pending = 'PENDING',
  Shipped = 'SHIPPED',
  Delivered = 'DELIVERED',
}
```

> **关于 any：** 写 `any` 等于把 TypeScript 退化成了 JavaScript。如果暂时不知道类型，用 `unknown`——它和 `any` 一样接受任何值，但用之前必须做类型收窄。

## 6.3 泛型

```typescript
// 泛型函数 — T 在调用时自动推断
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// 内置工具类型
type PartialUser = Partial<User>;        // 所有属性变可选
type UserPreview = Pick<User, 'id' | 'name'>;  // 只取部分属性
type ReadonlyUser = Readonly<User>;      // 所有属性变只读
type UserInput = Omit<User, 'id'>;       // 排除某些属性
```

## 6.4 type vs interface 选择指南

| 场景 | 使用 | 原因 |
|---|---|---|
| 定义对象形状 | `interface` | 可被 extend/implements，报错信息更清晰 |
| 联合类型、交叉类型 | `type` | interface 不支持联合类型 |
| 工具类型组合 | `type` | `Pick<A & B, 'x'>` 只能用 type |
| 函数签名 | `type` | `type Fn = (x: string) => void` 更简洁 |

## 6.5 类型导入与类型守卫

```typescript
// import type — 明确的类型导入，编译后会被完全移除
import type { User, ApiResponse } from '@/types';
import { userApi } from '@/api';  // 值导入，编译后保留

// 类型守卫 — 收窄 unknown 类型
function isUser(obj: unknown): obj is User {
  return typeof obj === 'object' && obj !== null && 'id' in obj;
}

// const assertion — 将字面量推断为最窄类型
const colors = ['red', 'green'] as const;
// 类型变为 readonly ["red", "green"]，而非 string[]
```
