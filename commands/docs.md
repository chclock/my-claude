---
description: Legacy slash-entry shim for the documentation-lookup skill. Prefer the skill directly.
---

# Docs 命令（遗留兼容层）

仅在仍使用 `/docs` 时使用此命令。主要的工作流程位于 `skills/documentation-lookup/SKILL.md`。

## 规范接口

- 直接使用 `documentation-lookup` skill。
- 仅将此文件作为兼容性入口点保留。

## 参数

`$ARGUMENTS`

## 委托

应用 `documentation-lookup` skill。
- 如果库或问题描述缺失，请询问缺失的部分。
- 使用 Context7 的实时文档而非训练数据。
- 只返回当前答案以及所需的最少代码或示例。
