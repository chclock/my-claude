---
description: Legacy slash-entry shim for the tdd-workflow skill. Prefer the skill directly.
---

# TDD 命令（旧版兼容桥接）

仅在仍在使用 `/tdd` 时使用此命令。主要的工作流程在 `skills/tdd-workflow/SKILL.md` 中。

## 标准入口

- 请直接使用 `tdd-workflow` 技能。
- 保留此文件仅作为兼容性入口。

## 参数

`$ARGUMENTS`

## 委托

应用 `tdd-workflow` 技能。
- 严格遵守红色 → 绿色 → 重构。
- 保持测试优先，覆盖率明确，检查点证据清晰。
- 使用该技能作为维护的 TDD 主体，而不是在此复制 playbook。
