---
description: Legacy slash-entry shim for the context-budget skill. Prefer the skill directly.
---

# Context Budget 优化器（遗留兼容层）

仅在仍在使用 `/context-budget` 时使用此命令。主要的工作流程位于 `skills/context-budget/SKILL.md`。

## 规范接口

- 直接使用 `context-budget` skill。
- 仅将此文件作为兼容性入口点保留。

## 参数

$ARGUMENTS

## 委托

应用 `context-budget` skill。
- 如果用户提供了 `--verbose`，则透传该参数。
- 假设上下文窗口为 200K，除非用户另有指定。
- 返回 skill 的清单、问题检测和优先级优化建议报告，无需在此重新实现扫描逻辑。K context window unless the user specified otherwise.
- Return the skill's inventory, issue detection, and prioritized savings report without re-implementing the scan here.
