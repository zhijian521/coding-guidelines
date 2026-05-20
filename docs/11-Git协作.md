# 十一、Git 协作

Git 是团队协作的骨架。分支混乱、commit message 随意，会让代码历史变得不可追溯。花几分钟写好 commit message，比三个月后翻 Git log 猜当时做了什么划算得多。

## 11.1 分支管理

| 分支 | 命名 | 说明 |
|---|---|---|
| 主分支 | `main` | 生产代码，禁止直接提交 |
| 开发分支 | `develop` | 开发主线 |
| 功能分支 | `feat/xxx` | 新功能开发 |
| 修复分支 | `fix/xxx` | Bug 修复 |
| 发布分支 | `release/x.x.x` | 版本发布准备 |
| 热修复 | `hotfix/x.x.x` | 生产紧急修复 |

## 11.2 Commit Message

采用 Conventional Commits 格式：`<type>(<scope>): <subject>`

```
feat(user): 添加用户登录功能
fix(order): 修复订单金额计算错误
docs(readme): 更新启动说明
refactor(api): 重构请求拦截器
perf(list): 优化列表渲染性能
chore(deps): 升级依赖版本
```

| Type | 说明 |
|---|---|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档变更 |
| `style` | 代码格式 |
| `refactor` | 代码重构 |
| `perf` | 性能优化 |
| `test` | 测试相关 |
| `chore` | 构建/工具/依赖 |

## 11.3 MR/PR 与 Code Review

| 项 | 要求 |
|---|---|
| MR 标题 | 与 Commit Message 格式一致：`feat(user): 添加登录功能` |
| MR 描述 | 写清楚做了什么、为什么这样做、如何测试 |
| 关联 Issue | 必须关联对应的任务或 Bug 编号 |
| Review 人数 | 至少 1 人 Review 通过后才能合并 |
| 冲突解决 | 谁拉的分支谁负责解决冲突，解决后重新请求 Review |

## 11.4 Rebase vs Merge

| 场景 | 策略 |
|---|---|
| 同步主分支最新代码到功能分支 | 用 `rebase`，保持提交历史线性 |
| 功能分支合并到主分支 | 用 `merge --no-ff`，保留分支轨迹 |
| 已推送到远程的分支 | 禁止 rebase，会破坏其他人的历史 |

> 合并分支使用 `git merge --no-ff` 保留历史。合并完成后删除已合并的分支。
>
> **.gitignore 必须包含：** `node_modules/` `.env.local` `dist/` `.DS_Store` `*.log` `.vscode/`（团队统一配置除外）。
