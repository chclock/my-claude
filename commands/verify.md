---
description: Legacy slash-entry shim for the verification-loop skill. Prefer the skill directly.
---

# 验证命令（旧版兼容桥接）

仅在仍在使用 `/verify` 时使用此命令。主要的工作流程位于 `skills/verification-loop/SKILL.md`。

## 标准入口

- 请直接使用 `verification-loop` 技能。
- 保留此文件仅作为兼容性入口。

## 参数

`$ARGUMENTS`

## 委托

应用 `verification-loop` 技能。
- 为用户请求的模式选择正确的验证深度。
- 按照当前仓库的正确顺序运行构建、类型、lint、测试、安全/日志检查和 diff 审查。
- 仅报告判定和阻碍，而不是在此处维护第二个验证检查清单。
