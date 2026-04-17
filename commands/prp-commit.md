---
description: Quick commit with natural language file targeting — describe what to commit in plain English
argument-hint: [target description] (blank = all changes)
---

# 智能提交

> 改编自 PRPs-agentic-eng by Wirasm。属于 PRP 工作流程系列。

**输入**：$ARGUMENTS

---

## 第一阶段 — 评估

```bash
git status --short
```

如果输出为空 → 停止：「没有需要提交的内容。」

向用户展示变更摘要（新增、修改、删除、未跟踪文件）。

---

## 第二阶段 — 解读并暂存

解读 `$ARGUMENTS` 以确定要暂存的内容：

| 输入 | 解读 | Git 命令 |
|---|---|---|
| *（空白）* | 暂存所有文件 | `git add -A` |
| `staged` | 使用已暂存的内容 | *（不执行 git add）* |
| `*.ts` 或 `*.py` 等 | 暂存匹配的文件 | `git add '*.ts'` |
| `except tests` | 暂存所有文件，然后取消暂存测试文件 | `git add -A && git reset -- '**/*.test.*' '**/*.spec.*' '**/test_*' 2>/dev/null || true` |
| `only new files` | 仅暂存未跟踪的文件 | `git ls-files --others --exclude-standard \| grep . && git ls-files --others --exclude-standard \| xargs git add` |
| `the auth changes` | 从状态/差异中解读 — 查找认证相关文件 | `git add <匹配的文件>` |
| 特定文件名 | 暂存这些文件 | `git add <文件>` |

对于自然语言输入（如「auth 相关的变更」），请交叉参照 `git status` 输出和 `git diff` 来识别相关文件。向用户展示您正在暂存哪些文件及其原因。

```bash
git add <已确定的文件>
```

暂存后，验证：
```bash
git diff --cached --stat
```

如果没有暂存任何文件，停止：「没有文件匹配您描述的内容。」

---

## 第三阶段 — 提交

以祈使语气撰写单行提交信息：

```
{类型}: {描述}
```

类型：
- `feat` — 新功能或能力
- `fix` — 错误修复
- `refactor` — 代码重构，不改变行为
- `docs` — 文档变更
- `test` — 添加或更新测试
- `chore` — 构建、配置、依赖
- `perf` — 性能改进
- `ci` — CI/CD 变更

规则：
- 使用祈使语气（「add feature」而非「added feature」）
- 类型前缀后小写
- 末尾不加句号
- 不超过 72 个字符
- 描述「什么」改变了，而非「如何」改变的

```bash
git commit -m "{类型}: {描述}"
```

---

## 第四阶段 — 输出

向用户报告：

```
已提交: {hash_short}
信息:   {类型}: {描述}
文件:   {count} 个文件已更改

后续步骤：
  - git push           → 推送到远程
  - /prp-pr            → 创建 Pull Request
  - /code-review       → 推送前审查
```

---

## 示例

| 您输入 | 发生什么 |
|---|---|
| `/prp-commit` | 暂存所有内容，自动生成信息 |
| `/prp-commit staged` | 仅提交已暂存的内容 |
| `/prp-commit *.ts` | 暂存所有 TypeScript 文件并提交 |
| `/prp-commit except tests` | 暂存除测试文件外的所有内容 |
| `/prp-commit the database migration` | 从状态中查找数据库迁移文件并暂存 |
| `/prp-commit only new files` | 仅暂存未跟踪的文件 |
