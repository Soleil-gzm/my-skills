# 拆分计划（多 commit）

目标：把当前工作区变成一组**各自独立可 review、可回滚、可 bisect** 的 commit。

## 拆分启发式

1. **一个 commit 一个意图**，不混无关变更
2. **机械性变更单独成 commit**：纯格式化、纯生成文件（除非仓库惯例相反）
3. **依赖变更保持内聚**：`package.json` 和它的 lockfile 同一个 commit；但不要和功能变更混
4. **爆炸半径最小**：横跨多领域的变更，考虑拆成"预备 refactor + 行为变更"两步（先问用户）
5. **未跟踪文件逐个定性**：提交 / gitignore / 删除
6. **monorepo**：不同无关联包的改动是强拆分信号

## 输出格式

给用户一份编号计划，每个 commit 含：**消息（完整格式）+ 包含的文件 + 分组理由/风险点**，并附上执行命令：

```markdown
## 拆分计划（3 个 commit）

### 1. feat(auth): add JWT login flow
- 文件：src/auth/jwt.ts, src/auth/middleware.ts, src/routes/login.ts
- 理由：本次核心功能

### 2. test(auth): add login flow tests
- 文件：tests/auth/login.test.ts
- 理由：测试可独立运行，与实现分开便于 review

### 3. style: format touched files with prettier
- 文件：src/auth/*.ts（纯格式化部分）
- 理由：机械变更隔离，不污染功能 diff

执行命令：
git add src/auth/jwt.ts src/auth/middleware.ts src/routes/login.ts && git commit -m "..."
git add tests/auth/login.test.ts && git commit -m "..."
git add -p   # 格式化与功能混在同一文件时逐块暂存
```

同一文件里混了多类变更 → 用 `git add -p` 逐 hunk 暂存。

## 确认选项

计划给出后必问：

- A：只暂存（stage only）
- B：暂存 + 提交
- C：暂存 + 提交 + 推送（仅当用户明确要 push）

## 质量标准（每个 commit 都要满足）

- 能独立通过测试/检查
- 能独立 revert 而不影响其他 commit
- 消息说清了意图和风险，不只是列文件
- 历史序列对 `git bisect` 友好

## 停止条件

- 一个 commit 混了多个无关行为变更 → 停下来先拆
- 某个 commit 无法独立验证 → 停下来问用户
- 发现已暂存内容里疑似敏感信息 → 转 security.md 流程
