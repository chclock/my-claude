---
description: Retrieve a Jira ticket, analyze requirements, update status, or add comments. Uses the jira-integration skill and MCP or REST API.
---

# Jira 命令

直接从您的工作流程中与 Jira 工单交互——获取工单、分析需求、添加评论和转换状态。

## 使用方法

```
/jira get <TICKET-KEY>          # 获取并分析工单
/jira comment <TICKET-KEY>      # 添加进度评论
/jira transition <TICKET-KEY>   # 更改工单状态
/jira search <JQL>              # 使用 JQL 搜索问题
```

## 此命令的作用

1. **获取 & 分析** — 获取 Jira 工单并提取需求、验收标准、测试场景和依赖
2. **评论** — 向工单添加结构化进度更新
3. **转换** — 将工单在工作流状态间移动（待办 → 进行中 → 完成）
4. **搜索** — 使用 JQL 查询查找问题

## 工作原理

### `/jira get <TICKET-KEY>`

1. 从 Jira 获取工单（通过 MCP `jira_get_issue` 或 REST API）
2. 提取所有字段：摘要、描述、验收标准、优先级、标签、关联问题
3. 可选择获取评论以获取额外上下文
4. 生成结构化分析：

```
Ticket: PROJ-1234
Summary: [title]
Status: [status]
Priority: [priority]
Type: [Story/Bug/Task]

需求:
1. [extracted requirement]
2. [extracted requirement]

验收标准:
- [ ] [criterion from ticket]

测试场景:
- 快乐路径: [description]
- 错误情况: [description]
- 边界情况: [description]

依赖:
- [linked issues, APIs, services]

推荐的下一步:
- /plan 创建实施计划
- /tdd 首先用测试进行实施
```

### `/jira comment <TICKET-KEY>`

1. 总结当前会话进度（构建了什么、测试了什么、提交了什么）
2. 格式化为结构化评论
3. 发布到 Jira 工单

### `/jira transition <TICKET-KEY>`

1. 获取工单可用的转换
2. 向用户显示选项
3. 执行选定的转换

### `/jira search <JQL>`

1. 对 Jira 执行 JQL 查询
2. 返回匹配问题的摘要表

## 先决条件

此命令需要 Jira 凭证。选择一种：

**选项 A — MCP 服务器（推荐）：**
将 `jira` 添加到您的 `mcpServers` 配置（有关模板，请参见 `mcp-configs/mcp-servers.json`）。

**选项 B — 环境变量：**
```bash
export JIRA_URL="https://yourorg.atlassian.net"
export JIRA_EMAIL="your.email@example.com"
export JIRA_API_TOKEN="your-api-token"
```

如果凭证缺失，停止并引导用户进行设置。

## 与其他命令的集成

分析工单后：
- 使用 `/plan` 从需求创建实施计划
- 使用 `/tdd` 用测试驱动开发进行实施
- 实施后使用 `/code-review`
- 使用 `/jira comment` 将进度发布回工单
- 工作完成后使用 `/jira transition` 移动工单

## 相关

- **技能:** `skills/jira-integration/`
- **MCP 配置:** `mcp-configs/mcp-servers.json` → `jira`
