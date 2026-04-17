---
description: Create hooks to prevent unwanted behaviors from conversation analysis or explicit instructions
---

# Hookify 命令

通过分析对话模式或明确的用户指令创建 hook 规则，以防止不良的 Claude Code 行为。

## 用法

`/hookify [要防止的行为描述]`

如果未提供参数，分析当前对话以发现有价值防止的行为。

## 工作流程

### 步骤一：收集行为信息

- 带参数：解析用户对不良行为的描述
- 无参数：使用 `conversation-analyzer` 代理查找：
  - 明确的纠正
  - 对重复错误的沮丧反应
  - 还原的更改
  - 重复的类似问题

### 步骤二：展示发现

向用户展示：

- 行为描述
- 建议的事件类型
- 建议的模式或匹配器
- 建议的操作

### 步骤三：生成规则文件

对于每个已批准的规则，在 `.claude/hookify.{name}.local.md` 创建文件：

```yaml
---
name: rule-name
enabled: true
event: bash|file|stop|prompt|all
action: block|warn
pattern: "regex pattern"
---
触发规则时显示的消息。
```

### 步骤四：确认

报告创建的规则以及如何使用 `/hookify-list` 和 `/hookify-configure` 管理它们。
