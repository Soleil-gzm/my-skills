---
name: git-commit
description: 分析 git 变更并生成规范的 Conventional Commits 提交：敏感文件防护、多变更拆分计划、type(scope) 判定、subject 英文 + body 中文。当用户说"提交/commit/帮我写 commit message/这些改动怎么拆 commit"时使用。Also triggers on: git commit, commit message, conventional commits, split commits, stage changes.
allowed-tools: Bash, Read, Write, Edit, Grep, Glob, AskUserQuestion
metadata:
  version: "1.0"
  category: git
---

# Git Commit — 提交助手

覆盖提交全流程：**安全检查 → 拆分计划 → 写消息 → 执行**。

## 语言约定（可改）

- **subject（第一行）：英文**，imperative mood，整行 ≤50 字符
- **body（正文）：中文**，解释 what/why，不解释 how（diff 本身展示 how）
- 纯个人项目想全中文：把上面两条都改成中文即可，规则不变

## 核心原则

1. **diff 是唯一事实来源。** 写消息前必须读 `git diff` / `git diff --cached`，禁止凭对话上下文猜改了什么。
2. **一个 commit 一个目的。** 无关变更混在一起的时候，先拆分（见 [references/splitting.md](references/splitting.md)）。
3. **敏感文件绝不入库。** 提交前过一遍防护清单（见 [references/security.md](references/security.md)）。
4. **执行前必须给用户看计划**，确认后才 `git add` / `git commit`。
5. **禁用 `git add -A` / `git add .`**，逐文件 add，防止带入无关或敏感文件。
6. 不擅自加任何 AI 署名（`Generated with ...`、`Co-Authored-By: ...`），除非用户明确要求。

## 标准流程

### Step 1：收集状态（只读）

```bash
git status --porcelain
git branch --show-current    # 为空 = detached HEAD，先提示建分支
git diff --stat
git diff --staged            # 已有 staged 内容时
git log --oneline -10        # 参考本仓库已有的消息风格，保持一致
```

有未跟踪文件 → 逐一定性：该提交 / 该 gitignore / 该删除（拿不准就问）。

### Step 2：安全与卫生检查

按 [references/security.md](references/security.md) 执行：敏感模式文件排除、.gitignore 补全、diff 内 secrets 扫描。发现问题立即提示，不静默放行。

### Step 3：判断要不要拆

出现以下信号 → 转 [references/splitting.md](references/splitting.md) 出拆分计划：
- 变更横跨多个不相关模块/包
- feat/fix 与纯格式化、纯生成文件混在一起
- `package.json` 与 lockfile 之外的依赖变更混进了功能变更
- 一个 commit 无法独立通过测试、独立回滚

### Step 4：写消息

规则细节见 [references/message-format.md](references/message-format.md)，骨架：

```
<type>(<scope>): <english imperative subject>   ← 整行 ≤50 字符，句尾无句号
                                                ← 空行
<body：中文，72 字符折行，解释 why>
                                                ← 空行
<footer：BREAKING CHANGE: ... / Closes #123>
```

type 速查（按"变更的主要目的"选，歧义时 `fix > feat > refactor > chore`）：

| type | 何时用 |
|------|--------|
| `feat` | 用户可见的新能力 |
| `fix` | 修 bug（顺带重构也算 fix）；修漏洞的依赖升级也算 fix |
| `refactor` | 不改行为的结构调整 |
| `docs` | 仅文档 |
| `test` | 仅测试 |
| `perf` | 性能优化 |
| `style` | 纯格式化 |
| `build` | 构建系统/依赖定义 |
| `ci` | CI/CD 配置 |
| `chore` | 以上都不沾的杂务 |
| `revert` | 回滚已有 commit |

详略按重要性分级：
- **feat / fix / perf / 安全**：subject + 中文 body（要点列表 + 影响说明）
- **refactor / test / build / ci**：subject + 1–2 句中文说明
- **docs / style / chore**：通常只有 subject

### Step 5：确认并执行

把完整消息贴给用户确认，然后用 heredoc 提交：

```bash
git commit -m "$(cat <<'EOF'
feat(auth): add JWT login flow

- 新增 token 签发与校验中间件
- 登录接口接入限流，防止暴力破解

Closes #42
EOF
)"
```

push 前必须单独问用户。amend 仅允许用于**未 push 的最后一个 commit**（漏文件、消息笔误），已 push 的绝不改写。

## 可选模式：会话级 changelog

用户明确要求"commit 时同时记 changelog"时，加载 [references/auto-changelog.md](references/auto-changelog.md)。默认**不启用**——日常 CHANGELOG.md 维护归 `project-doc-writer` skill 的场景④管，两边不重复。
