# 安全与卫生防护

提交前的防护网。**发现问题必须提示用户，禁止静默放行。**

## 文件三级分类

### 绝不提交（发现即排除 + 提示 + 检查 .gitignore）

- 凭据类：`*credentials*`、`*secret*`、`*password*`、`*.key`、`*.pem`、`.env`、`.env.local`、`.env.*.local`
- 工具本地配置：`.claude.json`、`.mcp.json*`、`settings.json`、`settings.local.json`、`.idea/workspace.xml`
- 构建产物：`node_modules/`、`vendor/`、`dist/`、`build/`、`coverage/`、`*.log`、`*.cache`
- OS 垃圾：`.DS_Store`、`Thumbs.db`、`desktop.ini`

### 一般安全（可直接提交）

- 文档：`*.md`、`docs/**`
- 源码：`src/**`、`lib/**`、`*_test.go`、`*.test.ts`
- 共享配置：`.gitignore`、`.editorconfig`、`tsconfig.json`、`go.mod`、`.github/workflows/**`

### 需要问用户

- **>1MB 的文件**：提示大小，问是否有意为之，必要时建议 Git LFS
- **新目录**：展示第一层内容，问用途再归类
- **可执行文件**（`*.exe`、`*.bin`）：问是构建产物还是要入库的工具
- 不在上面两类里的配置文件

## .gitignore 补全

发现"绝不提交"类文件没被 ignore 时：
1. 列出缺失的模式，说明每个的理由（安全 / 环境相关）
2. 用户确认后写入 `.gitignore`
3. 把 `.gitignore` 本身纳入本次提交

## diff 内 secrets 扫描

对 staged diff 扫一遍可疑模式：

```
API key 形态：[A-Za-z0-9_-]{20,} 的长字符串赋值
密码形态：password|passwd|pwd\s*[=:]
带凭据的 URL：://[^/]*:[^/]*@
私钥头：-----BEGIN .* PRIVATE KEY-----
```

命中 → 警告并阻止提交，给出处理建议（改用环境变量 / 从历史中清除）。

## 误提交 secret 的应急

1. **立即轮换该凭据**（删文件没用，历史里还在）
2. 再谈历史清理（`git filter-repo` 等）
3. 禁止用 force push "掩盖"——历史仍可恢复，且破坏协作者

## 常见错误状态处理

| 状态 | 检测 | 处理 |
|------|------|------|
| 合并冲突 | `git status` 有 "both modified" | 引导先解决冲突 |
| detached HEAD | `git branch --show-current` 为空 | 建议 `git switch -c <分支名>` |
| 无可提交内容 | `git status --porcelain` 为空 | 明确告知，提示 `git status` |
| pre-commit hook 失败 | commit 报错 | 修根本问题后**重新 commit**，不用 `--no-verify` 绕过 |
| TS 项目 | 有 tsconfig | 提交前先 `tsc --noEmit`，避免 hook 反复失败 |

## amend 规则

只允许：未 push 的最后一个 commit + 漏文件/消息笔误（`git commit --amend`）。
禁止：已 push、他人已基于该 commit 工作——那会改写共享历史。
