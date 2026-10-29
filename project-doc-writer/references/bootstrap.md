# 场景 ②：冷启动（无文档项目）

为有代码、无文档的项目生成第一版 README + docs/。**先检测项目类型，再选模板，禁止假设业务逻辑。**

## Step 1：确认现状

检查 `README.md`、`docs/`、`CONTRIBUTING.md` 是否存在：
- 已有完善文档 → 停止，转入场景 ④
- 有部分文档 → 问用户要补充什么
- 完全没有 → 继续

## Step 2：检测项目类型

按指示文件识别（用 Glob 找文件、Read 看依赖、Grep 确认 import）：

| 类型 | 指示文件 |
|------|---------|
| Django | `manage.py` + requirements 含 django |
| FastAPI | `main.py` + requirements 含 fastapi |
| Flask | `app.py` + requirements 含 flask |
| Express | `package.json` 含 express |
| NestJS | `nest-cli.json` |
| React | `package.json` 含 react + `.jsx/.tsx` |
| Vue | `vue.config.js` / `.vue` 文件 |
| Next.js | `next.config.js` + `pages/` |
| Angular | `angular.json` |
| Spring Boot | `pom.xml` / `build.gradle` |
| Rails | `Gemfile` 含 rails → **额外加载 [stacks/rails.md](stacks/rails.md)** |
| Python/Rust/Go/JS 库 | `pyproject.toml` / `Cargo.toml`+`src/lib.rs` / `go.mod` / `package.json` 有 `main` 字段且无 web 框架 |
| Terraform / K8s / Docker / Ansible | `.tf` / `kind:`+`apiVersion:` yaml / `Dockerfile` / `playbook.yml` |
| 全栈 | 前后端指示同时存在，或 monorepo（`apps/`、`packages/`、workspaces） |

## Step 3：生成 README.md

骨架原则见 SKILL.md（80–120 行落地页）。冷启动版本允许使用占位符：

- `{{PROJECT_NAME}}`、`{{INSTALL_COMMAND}}`、`{{RUN_COMMAND}}`、`{{TEST_COMMAND}}` → 自动从检测结果替换
- `[待补充]` → 留给用户填（如项目描述、徽章）

必备小节：标题+tagline → Overview（含检测到的技术栈）→ Installation（按框架给真实命令）→ Usage → Project Structure（真实目录树）→ Development（真实测试命令）→ License。

目录树用真实结构生成：

```bash
tree -L 3 -I 'node_modules|__pycache__|*.pyc|.git|dist|target'
```

## Step 4：生成 docs/

最少两页（后端/全栈三页）：

- `docs/architecture.md`：System Overview、组件（按真实目录）、数据流、技术栈、Design Decisions（占位）。架构图留 Mermaid 占位块，标注 `[待补充：替换为真实架构图]`
- `docs/api.md`（仅后端/全栈）：Overview、Authentication、Endpoints 表、Error Handling。**只写代码里真实存在的 endpoint，不确定就留 `[待补充]`**

## Step 5：报告

```markdown
## 冷启动完成

### 创建的文件
- README.md（X 行）
- docs/architecture.md（Y 行）
- docs/api.md（Z 行）

### 检测结果
- 类型：{Django 后端 / React 前端 / …}
- 框架 / 语言：{…}

### 需要你补充
- [ ] README 项目描述
- [ ] architecture.md 的 Mermaid 图
- [ ] api.md 的真实 endpoint 示例
```

## 禁区

- 不覆盖已有的完善文档
- 不生成编造的 API endpoint
- 不写泛泛而谈的"正确的废话"（如"本项目采用先进技术栈"）
