# 场景 ④：同步 / 保鲜

两类触发：代码刚变了要同步文档（changelog、README、行内注释）；文档放久了要刷新过期内容。

## 触发时机

做完以下事情后**主动提议**运行：实现新功能、修 bug、破坏性变更、新增 API、大重构、提交前。

不触发：typo 修复、进行中的半成品、无功能影响的琐碎清理。

## A. 变更同步流程

### Step 1：分析变更

```bash
git diff --name-only
git log --oneline -10
```

确定：改了什么 → 是否面向用户 → 是否破坏性。

### Step 2：更新 CHANGELOG.md

条目放 `## [Unreleased]` 下，按类别分组：

```markdown
## [Unreleased]

### Added
- 在 `path/to/file.rs:150-180` 新增 XXX，用于 …

### Changed
- **BREAKING**: `old_fn()` 更名为 `new_fn()`。迁移：…

### Fixed
- 修复 …（#issue号）

### Deprecated / Removed / Security
```

规则：现在时态（"Add"不是"Added"；中文直接陈述）；带文件路径便于跳转；破坏性变更必须给迁移说明；安全修复**不写漏洞细节**。

**分类与 commit prefix 对应表**（与 `git-commit` skill 同源，保持一致）：

| 变更性质 | CHANGELOG 类别 | commit prefix |
|---------|---------------|---------------|
| 新功能（面向用户） | Added | feat / add |
| bug 修复 | Fixed | fix |
| 仅文档 | （可不进 changelog） | docs |
| 无行为变化的重构 | Changed | refactor |
| 性能 | Changed | perf |
| 测试 | （可不进 changelog） | test |
| 工具/依赖/构建 | （可不进 changelog） | chore / build / ci |

### Step 3：更新相关 README

按变更范围选层级（根 README / 子系统 README / 组件 README）：
- 新能力 → 加进 Features 列表
- 安装步骤变了 → 更新 Getting Started
- 新 API / 配置项 → 更新对应小节并附示例
- 加到已有小节里，**不开重复小节**；风格与现存内容一致

### Step 4：补行内文档

以下必须有文档注释：所有 public 函数/方法、public 结构体/枚举/接口、public 模块、复杂算法、安全关键函数（必须写明安全不变量）。

不必写：private 实现细节、显而易见的 getter/setter、<5 行的平凡函数。

各语言格式要点：
- **Rust**：模块用 `//!`，函数/类型用 `///`，含 Arguments / Examples / Safety 小节
- **TypeScript/React**：JSDoc `/** */`，含 `@param` / `@returns` / `@example`
- **Ruby（Rails 项目见 stacks/rails.md）**：YARD `@param` / `@return` / `@raise`
- **Python**：docstring，含 Args / Returns / 算法说明

检测遗漏：

```bash
# Rust：无 /// 的 pub fn
grep -rn "pub fn" --include="*.rs" | grep -v "///"
# TypeScript：无 JSDoc 的 export
grep -rn "export function" --include="*.ts" --include="*.tsx" | grep -v "/\*\*"
```

### Step 5：修交叉引用

文件移动/改名后：更新 CLAUDE.md / AGENTS.md 里的链接、README 相对路径、其他 doc 页的引用。

### Step 6：验证

CHANGELOG 条目清晰完整、README 反映新功能、public API 均有注释、示例可运行、链接全部有效。

## B. 过期内容刷新

### 收集真实状态

- 实际目录树（跳过 `node_modules`、`__pycache__`、`.venv`、`target`、`dist`）
- `git log --oneline -20`
- 各类计数：如 `.bib` 条目数（`grep -c '@' *.bib`）、skills/ 下含 SKILL.md 的目录数
- 行数：`wc -l README.md CLAUDE.md docs/*.md`

### 过期检查

| 检查项 | 怎么发现 |
|--------|---------|
| 文件树 | 文档里的树块 vs 实际树 |
| 时间戳 | "最后更新"超过 7 天 |
| 各类计数 | 文档写的数字 vs 实际数字 |
| Next steps / 路线图 | 对照近期 commit，已完成却还写着"待做" |
| 占位符 | 标了 TODO/placeholder 但已有真实内容 |

### 瘦身审计

| 文件 | 阈值 | 处理 |
|------|------|------|
| README.md | >150 行 | 转场景③拆分 |
| CLAUDE.md / AGENTS.md | 单个 `##` 小节 >15 行参考性内容 | 抽到 docs/，留一行指针 |
| 重复内容 | CLAUDE.md 与 README/docs 重复 | 只留指针 |

### 应用规则

1. 先出报告分组列出发现，用户确认后才动手
2. 只用精准 Edit，绝不整文件重写
3. 只更新事实性/结构性内容；研究内容、设计决策、论证过程**不可动**
4. 完成的 next-step 用删除线 `~~条目~~`，不删除
5. 幂等：连跑两次，第二次应无变化
