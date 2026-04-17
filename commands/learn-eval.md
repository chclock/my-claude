---
description: "Extract reusable patterns from the session, self-evaluate quality before saving, and determine the right save location (Global vs Project)."
---

# /learn-eval - 提取、评估然后保存

在写入任何技能文件之前，通过质量门、保存位置决策和知识放置意识扩展 `/learn`。

## 提取什么

寻找：

1. **错误解决方案模式** — 根本原因 + 修复 + 可重用性
2. **调试技术** — 不明显的步骤、工具组合
3. **变通方案** — 库怪癖、API 限制、版本特定修复
4. **项目特定模式** — 约定、架构决策、集成模式

## 流程

1. 回顾会话以寻找可提取的模式
2. 识别最有价值/可重用的见解

3. **确定保存位置：**
   - 询问："这个模式在不同的项目中会有用吗？"
   - **全局**（`~/.claude/skills/learned/`）：可在 2+ 项目中使用的通用模式（bash 兼容性、LLM API 行为、调试技术等）
   - **项目**（当前项目中的 `.claude/skills/learned/`）：项目特定知识（特定配置文件的项目特定怪癖、架构决策等）
   - 如有疑问，选择全局（全局 → 项目比反向更容易）

4. 使用以下格式起草技能文件：

```markdown
---
name: pattern-name
description: "Under 130 characters"
user-invocable: false
origin: auto-extracted
---

# [Descriptive Pattern Name]

**Extracted:** [Date]
**Context:** [Brief description of when this applies]

## Problem
[What problem this solves - be specific]

## Solution
[The pattern/technique/workaround - with code examples]

## When to Use
[Trigger conditions]
```

5. **质量门 — 检查清单 + 综合判定**

   ### 5a. 必需检查清单（通过实际阅读文件进行验证）

   在评估草稿之前执行**所有**以下操作：

   - [ ] 按关键字 grep `~/.claude/skills/` 和相关项目 `.claude/skills/` 文件以检查内容重叠
   - [ ] 检查 MEMORY.md（项目和全局）是否有重叠
   - [ ] 考虑追加到现有技能是否足够
   - [ ] 确认这是可重用的模式，不是一次性修复

   ### 5b. 综合判定

   综合检查清单结果和草稿质量，然后选择**其中之一**：

   | 判定 | 含义 | 下一步操作 |
   |---------|---------|-------------|
   | **保存** | 独特、具体、范围适当 | 继续步骤 6 |
   | **改进后保存** | 有价值但需要改进 | 列出改进 → 修订 → 重新评估（一次）|
   | **吸收到 [X]** | 应追加到现有技能 | 显示目标技能和添加内容 → 步骤 6 |
   | **放弃** | 平凡、冗余或太抽象 | 解释推理并停止 |

   **指导维度**（为判定提供信息，不评分）：

   - **具体性和可操作性**：包含可立即使用的代码示例或命令
   - **范围适合性**：名称、触发条件和内容一致，专注于单一模式
   - **独特性**：提供现有技能未涵盖的价值（根据检查清单结果）
   - **可重用性**：未来会话中存在现实的触发场景

6. **判定特定确认流程**

   - **改进后保存**：呈现所需改进 + 修订草稿 + 更新后的检查清单/判定（一次重新评估后）；如果修订判定为**保存**，则在用户确认后保存，否则遵循新判定
   - **保存**：呈现保存路径 + 检查清单结果 + 1 行判定理由 + 完整草稿 → 用户确认后保存
   - **吸收到 [X]**：呈现目标路径 + 添加内容（diff 格式）+ 检查清单结果 + 判定理由 → 用户确认后追加
   - **放弃**：仅显示检查清单结果 + 推理（无需确认）

7. 保存 / 吸收到决定的位置

## 步骤 5 的输出格式

```
### Checklist
- [x] skills/ grep: no overlap (or: overlap found → details)
- [x] MEMORY.md: no overlap (or: overlap found → details)
- [x] Existing skill append: new file appropriate (or: should append to [X])
- [x] Reusability: confirmed (or: one-off → Drop)

### Verdict: Save / Improve then Save / Absorb into [X] / Drop

**Rationale:** (1-2 sentences explaining the verdict)
```

## 设计原理

此版本用基于检查清单的综合判定系统替换了之前的 5 维数字评分规则（Specificity、Actionability、Scope Fit、Non-redundancy、Coverage 评分为 1-5）。现代前沿模型（Opus 4.6+）具有强大的上下文判断能力——将丰富的定性信号强制转换为数字分数会丢失细微差别，可能产生误导性的总数。综合方法让模型自然地权衡所有因素，产生更准确的保存/放弃决策，而明确的检查清单确保不会跳过关键检查。

## 注意事项

- 不要提取微不足道的修复（拼写错误、简单语法错误）
- 不要提取一次性问题（特定的 API 中断等）
- 专注于未来会话中会节省时间的模式
- 保持技能专注——每个技能一个模式
- 当判定为吸收时，追加到现有技能而不是创建新文件
