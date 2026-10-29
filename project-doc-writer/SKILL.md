---
name: project-doc-writer
description: 项目文档全生命周期管理：规划新项目、为无文档项目冷启动、生成 README + docs/ 结构、随代码变更同步文档、刷新过期内容、跨多来源解释架构。当用户说"写文档/生成README/更新docs/同步文档/解释架构/项目规划"时使用。Also triggers on: create docs, write documentation, update docs, generate readme, sync docs, document project, project planning, explain architecture.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion
metadata:
  version: "1.0"
  category: documentation
---

# Project Doc Writer — 项目文档全生命周期

一个 SKILL 覆盖文档的五个阶段。**先判断当前处于哪个场景，再加载对应的 reference 文件**，不要一次全读。

## 场景路由

| 场景 | 触发信号 | 加载 |
|------|---------|------|
| ① 规划 | "我有个想法…"、"new project"、"项目规划"，还没有代码 | [references/plan.md](references/plan.md) |
| ② 冷启动 | 有代码但没有任何 README/docs | [references/bootstrap.md](references/bootstrap.md) |
| ③ 生成/重构 | "写文档"、"README 太长了帮我拆"、已有文档要补页面 | [references/structure.md](references/structure.md) |
| ④ 同步/保鲜 | 刚做完功能/修完 bug、"更新文档"、"refresh docs"、提交前 | [references/sync.md](references/sync.md) |
| ⑤ 解释 | "给我讲讲这个系统怎么工作的"、"为什么这样设计" | [references/explain.md](references/explain.md) |

拿不准时，用 AskUserQuestion 让用户选场景。

## 贯穿所有场景的核心原则

1. **先读代码再写文档。** 读 `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod` / `Gemfile`，扫 `src/` 结构。禁止编造功能、API、配置项。
2. **README 是落地页，不是手册。** 80–120 行：第一印象 + 安装 + 一个真实示例 + 指向 docs/ 的链接表。细节全部进 `docs/`，一页一个主题。
3. **改现有文档前必须给计划。** 列出要改什么，等用户确认。绝不静默重写、绝不直接删除内容（只能移动）。
4. **使用项目的语言。** 项目 README 是中文就写中文；代码标识符、命令、技术术语保留英文原文。
5. **保持现有风格。** 项目用 `├──` 树就不要换成 `|--`；标题层级、表格风格与现存文档一致。
6. **Rails 项目**额外加载 [references/stacks/rails.md](references/stacks/rails.md)（检测到 `Gemfile` 含 `rails` 时）。

## 文档骨架（场景 ②③ 的产物）

```
README.md               # 落地页，80–120 行
docs/
├── getting-started.md  # 必有：安装、启动、验证
├── architecture.md     # 有清晰架构时
├── api.md              # 暴露 API 时
├── configuration.md    # 有环境变量/配置文件时
├── deployment.md       # 有 Dockerfile/CI 时
├── contributing.md     # 开源/团队项目时
└── testing.md          # 有测试套件时
CHANGELOG.md            # 留在根目录（场景 ④ 维护）
```

每个 docs/ 页面：顶部 prev/next 导航行（顺序与 README 的 Documentation 表一致），底部 `## See Also` 链接 2–3 个相关页面。

## 与 git-commit skill 的关系

提交信息的 prefix 规则（feat/fix/docs/refactor/test/chore/perf/build/ci/style）由独立的 `git-commit` skill 负责。本 SKILL 只在场景 ④ 维护 CHANGELOG 时引用同一套 prefix 分类（见 [references/sync.md](references/sync.md)），不接管 commit 的生成与执行。
