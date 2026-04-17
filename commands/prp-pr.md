---
description: Create a GitHub PR from current branch with unpushed commits — discovers templates, analyzes changes, pushes
argument-hint: [base-branch] (default: main)
---

# 创建 Pull Request

> 改编自 PRPs-agentic-eng by Wirasm。属于 PRP 工作流程系列。

**输入**：`$ARGUMENTS` — 可选，可能包含基础分支名称和/或标志（如 `--draft`）。

**解析 `$ARGUMENTS`**：
- 提取任何已识别的标志（`--draft`）
- 将剩余的非标志文本作为基础分支名称
- 如果未指定，默认基础分支为 `main`

---

## 第一阶段 — 验证

检查前置条件：

```bash
git branch --show-current
git status --short
git log origin/<base>..HEAD --oneline
```

| 检查 | 条件 | 失败时操作 |
|---|---|---|
| 不在基础分支上 | 当前分支 ≠ 基础分支 | 停止：「请先切换到功能分支。」 |
| 工作区干净 | 没有未提交的变更 | 警告：「您有未提交的变更。请先提交或暂存。使用 `/prp-commit` 提交。」 |
| 有领先提交 | `git log origin/<base>..HEAD` 非空 | 停止：「相对于 `<base>` 没有领先提交。没有什么可以 PR 的。」 |
| 没有已存在的 PR | `gh pr list --head <branch> --json number` 为空 | 停止：「PR 已存在：#<number>。使用 `gh pr view <number> --web` 打开它。」 |

如果所有检查通过，继续。

---

## 第二阶段 — 发现

### PR 模板

按顺序搜索 PR 模板：

1. `.github/PULL_REQUEST_TEMPLATE/` 目录 — 如果存在，列出文件并让用户选择（或使用 `default.md`）
2. `.github/PULL_REQUEST_TEMPLATE.md`
3. `.github/pull_request_template.md`
4. `docs/pull_request_template.md`

如果找到，读取并使用其结构作为 PR 正文。

### 提交分析

```bash
git log origin/<base>..HEAD --format="%h %s" --reverse
```

分析提交以确定：
- **PR 标题**：使用带有类型前缀的常规提交格式 — `feat: ...`、`fix: ...` 等
  - 如果有多种类型，使用主要的类型
  - 如果是单个提交，直接使用其消息
- **变更摘要**：按类型/区域对提交分组

### 文件分析

```bash
git diff origin/<base>..HEAD --stat
git diff origin/<base>..HEAD --name-only
```

对变更文件进行分类：源代码、测试、文档、配置、迁移。

### PRP 产物

检查相关 PRP 产物：
- `.claude/PRPs/reports/` — 实施报告
- `.claude/PRPs/plans/` — 已执行的计划
- `.claude/PRPs/prds/` — 相关的 PRD

如果存在，在 PR 正文中引用这些。

---

## 第三阶段 — 推送

```bash
git push -u origin HEAD
```

如果由于分叉导致推送失败：
```bash
git fetch origin
git rebase origin/<base>
git push -u origin HEAD
```

如果 rebase 发生冲突，停止并通知用户。

---

## 第四阶段 — 创建

### 有模板

如果在第二阶段找到了 PR 模板，使用提交和文件分析填充每个部分。保留所有模板部分 — 如果不适用，将部分保留为「N/A」而不是删除。

### 无模板

使用此默认格式：

```markdown
## 摘要

<1-2 句描述此 PR 做什么以及为什么>

## 变更

<按区域分组的变更项目符号列表>

## 变更的文件

<变更文件列表或表格，带变更类型：已添加/已修改/已删除>

## 测试

<描述如何测试变更，或「需要测试」>

## 相关问题

<链接的问题，带 Closes/Fixes/Relates to #N，或「无」>
```

### 创建 PR

```bash
gh pr create \
  --title "<PR 标题>" \
  --base <基础分支> \
  --body "<PR 正文>"
  # 如果从 $ARGUMENTS 解析到了 --draft 标志，则添加 --draft
```

---

## 第五阶段 — 验证

```bash
gh pr view --json number,url,title,state,baseRefName,headRefName,additions,deletions,changedFiles
gh pr checks --json name,status,conclusion 2>/dev/null || true
```

---

## 第六阶段 — 输出

向用户报告：

```
PR #<number>：<标题>
URL：<url>
分支：<head> → <base>
变更：<changedFiles> 个文件中 +<additions> -<deletions>

CI 检查：<状态摘要或「待定」或「未配置」>

引用的产物：
  - <PR 正文中链接的任何 PRP 报告/计划>

后续步骤：
  - gh pr view <number> --web   → 在浏览器中打开
  - /code-review <number>       → 审查 PR
  - gh pr merge <number>        → 准备好时合并
```

---

## 边缘情况

- **没有 `gh` CLI**：停止并说明：「需要 GitHub CLI（`gh`）。安装：https://cli.github.com/」
- **未认证**：停止并说明：「请先运行 `gh auth login`。」
- **需要强制推送**：如果远程已分叉且已执行 rebase，使用 `git push --force-with-lease`（切勿使用 `--force`）。
- **多个 PR 模板**：如果 `.github/PULL_REQUEST_TEMPLATE/` 有多个文件，列出它们并让用户选择。
- **大型 PR（>20 个文件）**：警告 PR 大小。如果变更在逻辑上可分离，建议拆分。
