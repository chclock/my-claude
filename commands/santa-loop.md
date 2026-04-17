---
description: 对抗性双重审查收敛循环 — 两个独立的模型审查者必须在代码发布前都批准通过。
---

# Santa Loop

使用 santa-method 技能的对抗性双重审查收敛循环。两个独立审查者 — 不同的模型、无共享上下文 — 必须在代码发布前都返回 NICE。如果任一返回 NAUGHTY，则修复所有标记的问题，提交，然后重新运行新的审查者 — 最多 3 轮。

## 目的

对当前任务输出运行两个独立审查者（Claude Opus + 外部模型）。两者都必须在代码推送前返回 NICE。如果任一返回 NAUGHTY，则修复所有标记的问题，提交，然后重新运行新的审查者 — 最多 3 轮。

## 用法

```
/santa-loop [文件或 glob | 描述]
```

## 工作流程

### 第一步：确定审查范围

从 `$ARGUMENTS` 确定范围，或回退到未提交的变更：

```bash
git diff --name-only HEAD
```

读取所有变更文件以构建完整的审查上下文。如果 `$ARGUMENTS` 指定了路径、文件或描述，则使用该范围。

### 第二步：构建评分标准

根据正在审查的文件类型构建适当的评分标准。每个标准必须有客观的 PASS/FAIL 条件。至少包含：

| 标准 | 通过条件 |
|-----------|---------------|
| 正确性 | 逻辑健全，无 bug，处理边界情况 |
| 安全性 | 无秘密注入、XSS 或 OWASP Top 10 问题 |
| 错误处理 | 明确处理错误，无静默吞没 |
| 完整性 | 所有需求已解决，无遗漏情况 |
| 内部一致性 | 文件或章节之间无矛盾 |
| 无回归 | 变更不会破坏现有行为 |

根据文件类型添加特定领域标准（例如，TS 的类型安全、Rust 的内存安全、SQL 的迁移安全）。

### 第三步：双重独立审查

使用 Agent 工具**并行**启动两个审查者（在同一消息中以便并发执行）。两者必须完成后再进入裁决门。

每个审查者将每个评分标准评估为 PASS 或 FAIL，然后返回结构化 JSON：

```json
{
  "verdict": "PASS" | "FAIL",
  "checks": [
    {"criterion": "...", "result": "PASS|FAIL", "detail": "..."}
  ],
  "critical_issues": ["..."],
  "suggestions": ["..."]
}
```

裁决门（第四步）将这些映射到 NICE/NAUGHTY：两者都 PASS → NICE，任一 FAIL → NAUGHTY。

#### 审查者 A：Claude Agent（始终运行）

使用完整的评分标准 + 所有待审查文件启动一个 Agent（subagent_type：`code-reviewer`，model：`opus`）。提示必须包含：
- 完整的评分标准
- 待审查的所有文件内容
- "你是一个独立的quality审查者。你没有看到过任何其他审查。你的工作是发现问题，而不是批准。"
- 返回上面的结构化 JSON 裁决

#### 审查者 B：外部模型（仅在未安装外部 CLI 时使用 Claude 回退）

首先，检测哪些 CLI 可用：
```bash
command -v codex >/dev/null 2>&1 && echo "codex" || true
command -v gemini >/dev/null 2>&1 && echo "gemini" || true
```

构建审查者提示（与审查者 A 相同的评分标准 + 说明）并写入唯一的临时文件：
```bash
PROMPT_FILE=$(mktemp /tmp/santa-reviewer-b-XXXXXX.txt)
cat > "$PROMPT_FILE" << 'EOF'
... 完整评分标准 + 文件内容 + 审查者说明 ...
EOF
```

使用首个可用的 CLI：

**Codex CLI**（如果已安装）
```bash
codex exec --sandbox read-only -m gpt-5.4 -C "$(pwd)" - < "$PROMPT_FILE"
rm -f "$PROMPT_FILE"
```

**Gemini CLI**（如果已安装且没有 codex）
```bash
gemini -p "$(cat "$PROMPT_FILE")" -m gemini-2.5-pro
rm -f "$PROMPT_FILE"
```

**Claude Agent 回退**（仅在未安装 codex 和 gemini 时）
启动第二个 Claude Agent（subagent_type：`code-reviewer`，model：`opus`）。记录警告：两个审查者共享相同的模型系列 — 未实现真正的模型多样性，但上下文隔离仍然强制执行。

在所有情况下，审查者必须返回与审查者 A 相同的结构化 JSON 裁决。

### 第四步：裁决门

- **两者都 PASS** → **NICE** — 进入第六步（推送）
- **任一 FAIL** → **NAUGHTY** — 合并两个审查者的所有关键问题，去重，进入第五步

### 第五步：修复循环（NAUGHTY 路径）

1. 显示两个审查者的所有关键问题
2. 修复每个标记的问题 — 只更改标记的内容，不做顺便重构
3. 在单个提交中提交所有修复：
   ```
   fix: address santa-loop review findings (round N)
   ```
4. 使用**新的审查者**重新运行第三步（无前一轮的记忆）
5. 重复直到两者都返回 PASS

**最多 3 次迭代。** 如果 3 轮后仍是 NAUGHTY，停止并呈现剩余问题：

```
SANTA LOOP 升级（超过 3 次迭代）

3 轮后剩余问题：
- [列出两个审查者所有未解决的关键问题]

需要人工审核后才能继续。
```

不要推送。

### 第六步：推送（NICE 路径）

当两个审查者都返回 PASS 时：

```bash
git push -u origin HEAD
```

### 第七步：最终报告

打印输出报告（见下文输出部分）。

## 输出

```
SANTA 裁决：[NICE / NAUGHTY（已升级）]

审查者 A（Claude Opus）：   [PASS/FAIL]
审查者 B（使用的模型）：  [PASS/FAIL]

一致性：
  两者都标记：      [两者都发现的问题]
  仅审查者 A 标记：[仅 A 发现的问题]
  仅审查者 B 标记：[仅 B 发现的问题]

迭代次数：[N]/3
结果：     [已推送 / 已升级给用户]
```

## 注意事项

- 审查者 A（Claude Opus）始终运行 — 保证无论工具如何至少有一个强力审查者。
- 模型多样性是审查者 B 的目标。GPT-5.4 或 Gemini 2.5 Pro 带来真正的独立性 — 不同的训练数据、不同的偏见、不同的盲点。仅 Claude 回退仍然通过上下文隔离提供价值，但失去了模型多样性。
- 使用最强的可用模型：审查者 A 用 Opus，审查者 B 用 GPT-5.4 或 Gemini 2.5 Pro。
- 外部审查者使用 `--sandbox read-only`（Codex）运行以防止审查期间仓库被修改。
- 每轮使用新的审查者可防止先前发现的锚定偏差。
- 评分标准是最重要的输入。如果审查者橡皮图章或标记主观样式问题，请收紧标准。
- 在 NAUGHTY 轮次提交，以便在循环中断时保留修复。
- 仅在 NICE 后推送 — 永远不要在循环中途推送。
