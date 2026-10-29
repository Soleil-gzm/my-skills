# 场景 ①：项目规划

把一句"项目想法"展开成两份文档，放在 `project-management/` 下。此时代码还不存在，**不要**去扫代码库，信息全部来自用户描述；缺关键信息就用 AskUserQuestion 问，或标 `[TODO: 待定]`。

## 产物

### 1. PROJECT-OVERVIEW.md（高层愿景，≤2 页）

```markdown
# {项目名}

> {一句话 tagline}

## Vision
{2–3 句：最终目标和影响}

## Goals
1. {可衡量的主要目标}
2. …

## Key Features
- **{特性1}**：{一句话描述}

## Success Criteria
1. **{指标}**：{数值目标，如 "<200ms p95 响应时间"}

## Constraints
- **Timeline / Budget / Technology / Team**：{已知约束}

## Out of Scope
- {明确不做什么、留到什么阶段}
```

### 2. SPECIFICATIONS.md（技术规格，可较长）

按需包含以下模块，不适用的直接省略：

- **Functional Requirements**：每个特性 = 描述 + User Story（As a … I want … so that …）+ Acceptance Criteria 勾选框
- **User Flows**：编号步骤（User 动作 → System 响应 → …）
- **Non-Functional Requirements**：Performance / Security / Reliability / Scalability，全部给数值
- **API Contracts**：每个 endpoint 给 Request / Response(200) / Error 的 JSON 示例
- **Data Models**：字段、类型、约束、外键关系
- **System Architecture**：组件列表 + 部署形态
- **Dependencies**：按 backend / frontend / infra 分组，带版本
- **Development Phases**：按 2–3 个月切块，勾选框
- **Testing Strategy**：unit（覆盖率目标）/ integration / E2E
- **Security Considerations**：对照 OWASP Top 10 逐条说明
- **Monitoring & Observability**：指标（RED）、日志、告警阈值

## 生成流程

1. 从用户描述中提取：项目名、特性、技术栈偏好、约束、成功指标
2. 写 PROJECT-OVERVIEW.md（项目语言；中文提问就用中文）
3. 写 SPECIFICATIONS.md
4. 对照校验：Vision 是否 2–3 句、Goals 是否可衡量、Success Criteria 是否含数值、非功能需求是否齐全
5. 报告：文件路径 + 大小 + 建议的下一步（如"接下来进入场景 ②/③ 生成代码文档"）

## 不做的事

- 不生成代码
- 不替用户做架构决策（给模板，用户决定）
- 不拆 user story
