---
description: Legacy slash-entry shim for the nanoclaw-repl skill. Prefer the skill directly.
---

# Claw 命令（旧版兼容桥接）

仅在仍然习惯性地使用 `/claw` 时使用此命令。主要实现位于 `skills/nanoclaw-repl/SKILL.md` 中。

## 标准入口

- 请直接使用 `nanoclaw-repl` 技能。
- 保留此文件仅作为兼容性入口，同时逐步淘汰命令优先的使用方式。

## 参数

`$ARGUMENTS`

## 委托

应用 `nanoclaw-repl` 技能，并将响应集中在操作或扩展 `scripts/claw.js` 上。
- 如果用户想运行它，请使用 `node scripts/claw.js` 或 `npm run claw`。
- 如果用户想扩展它，请保留零依赖和 markdown 支持的会话模型。
- 如果请求实际上是关于长时间运行的编排而不是 NanoClaw 本身，请重定向到 `dmux-workflows` 或 `autonomous-agent-harness`。