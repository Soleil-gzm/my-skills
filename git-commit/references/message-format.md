# 消息格式细则

## 结构

```
<type>(<scope>): <subject>     ← 整行 ≤50 字符（含 type 和 scope）
                                ← 空行
<body>                          ← 72 字符折行，解释 why 而非 how
                                ← 空行
<footer>                        ← BREAKING CHANGE / Closes #123
```

## subject 硬性规则

1. **英文 imperative mood**——"add" 不是 "added/adds"。自检句式："If applied, this commit will ___"
2. **≤50 字符**，算的是整行（`feat(auth):` 已占 13 字符，只剩 37 给描述）
3. 冒号后小写、句尾无句号
4. **具体**——"fix null pointer in user lookup"，不是 "fix bug"

## scope 规则

- 小写，用**模块/特性名**，不用文件路径：`auth` 而非 `src/middleware/auth.ts`
- **monorepo**（`packages/<名>/`、`apps/<名>/`、`services/<名>/`）：
  - 改动都在一个包内 → scope 用包名：`feat(auth-service): ...`
  - 跨包但同一关注点 → 用特性名：`feat(oauth): ...`
  - 跨包且无关联 → **拆 commit**，不要硬写
- 改动太散 → 省略 scope

## type 歧义判定

按"主要目的"选，拿不准时优先级 `fix > feat > refactor > chore`：

| 场景 | type |
|------|------|
| 新文件 + 新函数 | feat |
| 修 bug 顺带重构 | fix |
| 写功能时顺手加的测试 | feat（测试不单独算） |
| 修漏洞的依赖升级 | fix（不是 build） |
| 重命名 public API | refactor + `BREAKING CHANGE` footer |
| 纯文档 | docs；代码+文档混合 → 按代码定 type |

## 从 diff 识别 type 的模式

- **feat**：新文件（尤其 src/components/api 下）、新增 `export function/class`、新路由；新增 20+ 行通常是 feat
- **fix**：新增条件分支/校验、新增 `try/catch/throw`、测试复现 bug
- **refactor**：增删行数接近、函数搬家/改名、测试覆盖不变
- **docs**：只有 `.md`/README/CHANGELOG/docs/
- **test**：`*.test.*`、`*.spec.*`、`__tests__/`
- **style**：`.css/.scss`、prettier/eslint 产出、纯空白变化
- **chore/build**：`package.json`、lockfile、`.gitignore`、配置文件

## body 详略分级

| 级别 | 适用 type | body 要求 |
|------|----------|----------|
| 详细 | feat / fix / perf / 安全 | 中文要点列表（2–5 条）+ 一句影响说明 |
| 简要 | refactor / test / build / ci | 1–2 句中文 |
| 最简 | docs / style / chore | 可只有 subject |

## footer

- 破坏性变更：`BREAKING CHANGE: 什么坏了 + 怎么迁移`（全大写），或 subject 用 `!`：`feat(api)!: remove v1 endpoints`
- issue 关联：`Closes #123`、`Refs #456`
- 安全修复不在公开消息里写漏洞细节

## 好 / 坏对照

```
✅ feat(auth): add JWT login flow
✅ fix(utils): correct off-by-one in month formatting
✅ chore(deps): bump eslint and typescript

❌ update / fix bug / changes           （太模糊）
❌ feat: Added some new features        （过去时 + 大写 + 模糊）
❌ FEAT: Add Feature.                   （大小写错 + 句号）
❌ feat(src/middleware/auth.ts): ...    （路径当 scope）
```

## 执行前检查单

- [ ] 读过 `git diff --cached`，消息与 diff 一致（不编造）
- [ ] type 依据能说清；scope 是模块名不是路径
- [ ] subject ≤50 字符、imperative、无句号
- [ ] body 用中文讲清了 why（非平凡变更）
- [ ] 公共 API 变了 → 有 BREAKING CHANGE
- [ ] 无 AI 署名（除非用户要求）
- [ ] 无混入的无关变更
