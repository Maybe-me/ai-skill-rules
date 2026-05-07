---
name: git-commit
description: Git 提交流程。用于在代码变更完成后，规范地完成暂存、提交信息编写与推送。适用于任何语言和项目类型。可由 feature-dev-*、bug-fix-*、refactor-* 等 Skill 末尾衔接触发，也可单独调用。
---

# Git 提交 SOP

> 当代码变更已完成并通过自检，准备纳入版本管理时使用。
> 可由其他 Skill（如 `feature-dev-*`、`bug-fix-*`、`refactor-*`）末尾衔接触发，也可单独调用。

---

## 阶段一：变更确认

### 步骤 1：查看当前变更状态

```bash
git status
git diff
```

确认以下内容：
- 变更文件是否符合预期
- 是否有不应提交的临时文件、调试代码或本地配置
- 是否有遗漏未保存的改动

### 步骤 2：清理不应提交的内容

- 删除或忽略调试日志、临时文件、IDE 生成文件
- 确认 `.gitignore` 已覆盖敏感文件（`.env`、`*.key`、`*_secret*` 等）
- **禁止**将密钥、Token、密码等凭据纳入提交

---

## 阶段二：暂存文件

### 步骤 3：选择性暂存

优先精确暂存，避免 `git add .` 误纳入无关变更：

```bash
# 推荐：按文件暂存
git add path/to/file1 path/to/file2

# 或交互式逐块确认
git add -p

# 全部暂存（仅在确认无污染文件时使用）
git add .
```

暂存后再次确认内容：

```bash
git diff --staged
```

---

## 阶段三：编写提交信息

### 步骤 4：选择提交类型

按本次变更的主要意图选择：

| 类型 | 适用场景 |
|------|---------|
| `feat` | 新增功能、接口、能力 |
| `fix` | 修复 Bug 或错误行为 |
| `refactor` | 重构，不改变外部行为 |
| `test` | 新增或修改测试 |
| `docs` | 仅文档变更 |
| `chore` | 构建脚本、依赖管理、配置工具（不涉及业务代码） |
| `style` | 代码格式调整（不影响逻辑） |
| `perf` | 性能优化 |
| `ci` | CI/CD 配置变更 |
| `build` | 构建系统或外部依赖变更 |
| `revert` | 回滚某次提交 |

### 步骤 5：编写提交信息

格式遵循 [Conventional Commits](https://www.conventionalcommits.org/)：

```
<type>(<scope>): <subject>

[body]

[footer]
```

**规则：**
- `subject`：祈使句，首字母小写，不加句号，不超过 72 个字符
- `scope`：可选，括号括起，指明影响范围（如模块名、包名、文件名）
- `body`：可选，解释"为什么这样做"，而不是"做了什么"
- `footer`：可选，引用 Issue（`Closes #123`）或声明破坏性变更（`BREAKING CHANGE: ...`）

**示例：**

```
feat(user): add email verification on registration

Adds a verification step to prevent fake account creation.
Users receive an email link valid for 24 hours.

Closes #42
```

```
fix(payment): handle nil pointer when order amount is zero
```

```
refactor(repo): extract common query builder to shared util
```

### 步骤 6：执行提交

```bash
# 单行提交信息
git commit -m "<type>(<scope>): <subject>"

# 或打开编辑器编写多行提交信息
git commit
```

---

## 阶段四：推送（可选）

### 步骤 7：推送到远端

```bash
# 当前分支推送
git push

# 首次推送新分支
git push -u origin <branch-name>
```

推送前确认：
- 当前分支是否正确（`git branch`）
- 是否需要先同步最新变更（`git pull --rebase`）
- 目标分支是否有保护规则（需通过 PR/MR 合并）

### 步骤 8：提交后检查

```bash
git log --oneline -5
```

确认提交记录清晰可读，提交信息符合预期。

---

## 快速参考

### 上游 Skill 与提交类型映射

| 上游 Skill | 建议提交类型 |
|------------|------------|
| `feature-dev-*` | `feat` |
| `bug-fix-*` | `fix` |
| `refactor-*` | `refactor` |
| `testing-*` | `test` |
| `deploy-doc-*` / `api-doc-*` | `docs` |
| `dev-review-*`（含代码修复） | 根据实际修改类型选择 |

### 多类型变更拆分原则

- 一次提交只做一件事
- 若本次变更同时包含 `feat` 和 `fix`，优先拆为两次提交
- 若确实难以拆分，选择影响更大的类型，在 `body` 中补充说明
