---
description: Legacy slash-entry shim for the claude-devfleet skill. Prefer the skill directly.
---

# DevFleet（旧版兼容桥接）

仅在仍然使用 `/devfleet` 时使用此命令。主要的工作流程在 `skills/claude-devfleet/SKILL.md` 中。

## 标准入口

- 请直接使用 `claude-devfleet` 技能。
- 保留此文件仅作为兼容性入口，同时逐步淘汰命令优先的使用方式。

## 参数

`$ARGUMENTS`

## 委托

应用 `claude-devfleet` 技能。
- 根据用户的描述进行规划，显示 DAG，并在分派前获得批准，除非用户已经表示继续。
- 对于长时间任务，优先使用轮询状态而不是阻塞等待。
- 从结构化的任务报告中报告任务 ID、更改的文件、失败和后续步骤。