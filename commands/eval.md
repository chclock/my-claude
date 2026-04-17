---
description: Legacy slash-entry shim for the eval-harness skill. Prefer the skill directly.
---

# Eval 命令（旧版兼容桥接）

仅在仍然使用 `/eval` 时使用此命令。主要的工作流程在 `skills/eval-harness/SKILL.md` 中。

## 标准入口

- 请直接使用 `eval-harness` 技能。
- 保留此文件仅作为兼容性入口。

## 参数

`$ARGUMENTS`

## 委托

应用 `eval-harness` 技能。
- 支持与以前相同的用户意图：定义、检查、报告、列出和清理。
- 保持评估能力优先、回归支持并基于证据。
- 使用技能作为标准评估器，而不是维护单独的命令专用攻略本。