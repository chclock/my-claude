---
description: Enable or disable hookify rules interactively
---

# Hookify 配置命令

交互式地开启或关闭现有的 hookify 规则。

## 步骤

1. 查找所有 `.claude/hookify.*.local.md` 文件
2. 读取每个规则的当前状态
3. 展示列表及当前的启用/禁用状态
4. 询问要切换的规则
5. 更新所选规则文件中的 `enabled:` 字段
6. 确认更改
