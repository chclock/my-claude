---
description: Legacy slash-entry shim for the agent-sort skill. Prefer the skill directly.
---

# Agent Sort（旧版兼容桥接）

仅在仍在使用 `/agent-sort` 时使用此命令。主要的工作流程在 `skills/agent-sort/SKILL.md` 中。

## 标准入口

- 请直接使用 `agent-sort` 技能。
- 保留此文件仅作为兼容性入口。

## 参数

`$ARGUMENTS`

## 委托

应用 `agent-sort` 技能。
- 使用具体的仓库证据对 ECC 表面进行分类。
- 将结果保留为 DAILY 或 LIBRARY。
- 如果之后需要安装更改，请转交给 `configure-ecc`，而不是在此重新实现安装逻辑。
