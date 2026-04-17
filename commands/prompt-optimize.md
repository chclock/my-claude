---
description: Legacy slash-entry shim for the prompt-optimizer skill. Prefer the skill directly.
---

# Prompt Optimize（传统兼容层）

如果您仍在使用 `/prompt-optimize`，请使用此命令。主要的工作流程位于 `skills/prompt-optimizer/SKILL.md`。

## 标准入口

- 直接使用 `prompt-optimizer` skill。
- 仅将此文件作为兼容性入口保留。

## 参数

`$ARGUMENTS`

## 委托

应用 `prompt-optimizer` skill。
- 仅提供建议：优化提示词，不执行任务。
- 返回推荐的 ECC 组件以及可直接使用的提示词。
- 如果用户实际想要直接执行，请说明情况并告诉他们改用普通任务请求，而不是继续停留在兼容层中。
