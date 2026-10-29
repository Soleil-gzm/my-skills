# 可选模式：会话级 changelog

仅当用户明确要求"commit 时记 changelog"时启用。与 `project-doc-writer` 场景④的 CHANGELOG.md 维护不同：这里是**每次工作会话一篇独立档案**，可搜索、按关键词索引。

## 首次使用初始化

`changelog/` 不存在时创建：

```
changelog/
├── .changelog-keywords.txt   # 关键词词表
└── README.md                 # 自动生成的索引（不要手改）
```

## 每次提交的流程

1. `git status` / `git diff --stat` 确认有变更
2. 按文件路径建议关键词（见下表）
3. 问用户：本次做了什么 / 为什么 / 遇到什么问题（可选）/ 关键词确认 / 依赖变化 / 测试情况 / 后续事项
4. 生成 `changelog/YYYY-MM-DD-HHMM-<slug>.md`（时间戳防分支冲突）
5. 重建 README 索引：按月份分组 + 底部关键词索引
6. 代码 + changelog 一起提交

## 关键词建议表

| 文件模式 | 建议关键词 |
|---------|-----------|
| `**/migrations/*.sql`、`**/models/**` | [DATABASE] |
| `**/api/**` | [BACKEND] [API] |
| `**/components/**`、`**/pages/**` | [FRONTEND] [UI] |
| `*.css`、`**/styles/**` | [STYLING] |
| `package.json` 等依赖清单 | [DEPENDENCIES] |
| `*.test.*`、`**/__tests__/**` | [TESTING] |
| `README*`、`docs/**` | [DOCUMENTATION] |
| `Dockerfile`、`*.yml` | [DEPLOYMENT] [INFRA] |

关键词全大写，用户可增删。

## 条目模板

```markdown
# YYYY-MM-DD - 标题
**Keywords:** [KEYWORD1] [KEYWORD2]
**Commit:** <sha>

## What Changed
- `path/to/file`：加了什么 / 改了什么

## Why
{决策原因——最重要的一节}

## Issues Encountered
{踩过的坑和 workaround，失败的实验也值得记}

## Dependencies
{新增/升级的包}

## Testing Notes
- 测了什么 / 没测什么 / 边界情况

## Next Steps
- [ ] 事项
```

## 规则

- 条目**不可变**，创建后不改；同一天继续同一特性时可询问是否 amend 最后一条
- README 索引是生成物，不手改
- 琐碎变更可跳过："commit without changelog: fix typo"
- 一次工作会话一条目（连续多个小 commit 不必每个都记）
