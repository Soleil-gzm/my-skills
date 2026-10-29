# 场景 ③：生成 / 重构文档结构

适用于：从零生成、把超长 README 拆进 docs/、审计并改进已有文档、收拢散落在根目录的 md 文件。

## 先判断状态

```
状态 A：无 README.md            → 从零生成（流程同场景②，但按本文件的模板细节执行）
状态 B：有 README.md，无 docs/  → 拆分
状态 C：README.md + docs/ 都有  → 审计改进
```

## README 模板（落地页，80–120 行）

```markdown
# 项目名

> 一句话 tagline。

2–3 句：这是什么、为什么存在。

## Quick Start

​```bash
# 1–3 条真实安装命令（从包管理器检测）
​```

## Key Features

- **特性 1** — 一句话
- **特性 2** — 一句话

## Example

​```
# 一个真实用法示例——用户在这里决定"我要用它"
​```

## Documentation

| 指南 | 说明 |
|------|------|
| [Getting Started](docs/getting-started.md) | 安装、配置、第一步 |
| [Architecture](docs/architecture.md) | 项目结构与模式 |

## License

MIT
```

**禁止**：长段落描述、完整 API 参考、配置细节。这些进 docs/。

## docs/ 页面模板

```markdown
[← 上一页](prev.md) · [返回 README](../README.md) · [下一页 →](next.md)

# 主题标题

内容按小节组织，配代码示例和表格。每个小节自包含。

## See Also

- [相关主题 1](related.md) — 一句话
```

- 导航顺序 = README Documentation 表从上到下的顺序；第一页无 "← 上一页"，最后一页无 "下一页 →"
- 跨文件链接一律用相对路径

## 各主题内容要点

- **getting-started.md**（必有）：前置依赖版本 → 分步安装 → 首次运行 → 验证预期输出 → 下一步链接
- **architecture.md**：高层概述（可配图）→ 目录结构及解释 → 关键模式（命名、错误处理）→ 数据流
- **api.md**：Base URL → 认证 → 按资源分组的 endpoint → 请求/响应示例 → 错误码
- **configuration.md**：全部环境变量（含默认值）→ 配置文件用途 → feature flags
- **deployment.md**：构建步骤 → 环境准备 → CI/CD → 监控/健康检查

## 状态 B：拆分超长 README（>150 行）

**留在 README**：标题/tagline/徽章、Key Features、Quick install（1–3 条）、简短示例、Documentation 表、License。

**移入 docs/**：详细安装 → getting-started；架构/目录 → architecture；完整 API → api；配置细节 → configuration；贡献指南 → contributing；任何 >30 行的单一主题小节。

流程：分析 → 给用户看拆分计划 → 确认后执行 → **逐节核对无内容丢失**。

## 状态 C：审计清单

- README 是否仍是落地页（<150 行）
- 是否有未文档化的主题（如发现 12 个 endpoint 但没有 api.md）
- 是否引用了已不存在的文件 / API / 环境变量
- 每个 docs/ 页是否有导航头 + See Also
- 内部链接是否全部有效
- 格式风格是否一致

输出审计报告（✅/⚠️/❌ 表格 + 修复计划）→ 用户确认后执行。

## 收拢根目录散落的 md 文件

| 根目录文件 | 目标 | 方式 |
|-----------|------|------|
| CONTRIBUTING.md | docs/contributing.md | 移动 |
| ARCHITECTURE.md | docs/architecture.md | 移动 |
| DEPLOYMENT.md | docs/deployment.md | 移动 |
| SETUP.md / DEVELOPMENT.md | docs/getting-started.md | 合并追加 |
| API.md / TESTING.md / SECURITY.md | docs/同名小写 | 移动 |

**留在根目录**：README.md、CHANGELOG.md、LICENSE、CODE_OF_CONDUCT.md。

流程：列出文件 + 行数 + 摘要 → 给出移动/合并计划 → 用户确认 → 执行（原文件**暂不删**）→ 更新 README Documentation 表和旧链接 → 审查通过后**询问**是否删除原文件。绝不自动删除。
