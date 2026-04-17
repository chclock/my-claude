---
description: Load the most recent session file from ~/.claude/session-data/ and resume work with full context from where the last session ended.
---

# 恢复会话命令

加载上次保存的会话状态并在执行任何工作之前完全定位。
此命令是 `/save-session` 的对应命令。

## 使用场景

- 开始新会话来继续前一天的工作
- 由于上下文限制而开始新的会话之后
- 从其他来源移交会话文件时（只需提供文件路径）
- 任何时候您有会话文件并希望 Claude 在继续之前完全吸收它

## 使用方法

```
/resume-session                                                      # 加载 ~/.claude/session-data/ 中最新的文件
/resume-session 2024-01-15                                           # 加载该日期的最新会话
/resume-session ~/.claude/session-data/2024-01-15-abc123de-session.tmp  # 加载当前的短 ID 会话文件
/resume-session ~/.claude/sessions/2024-01-15-session.tmp               # 加载特定的旧格式文件
```

## 流程

### 步骤 1: 找到会话文件

如果没有提供参数：

1. 检查 `~/.claude/session-data/`
2. 选择最近修改的 `*-session.tmp` 文件
3. 如果文件夹不存在或没有匹配的文件，告诉用户：
   ```
   在 ~/.claude/session-data/ 中找不到会话文件
   在会话结束时运行 /save-session 来创建一个。
   ```
   然后停止。

如果提供了参数：

- 如果看起来像日期（`YYYY-MM-DD`），首先搜索 `~/.claude/session-data/`，然后搜索旧版
  `~/.claude/sessions/`，查找匹配 `YYYY-MM-DD-session.tmp`（旧格式）或
  `YYYY-MM-DD-<shortid>-session.tmp`（当前格式）的文件
  并加载该日期最近修改的变体
- 如果看起来像文件路径，直接读取该文件
- 如果找不到，清楚地报告并停止

### 步骤 2: 阅读整个会话文件

阅读完整文件。暂时不总结。

### 步骤 3: 确认理解

使用以下确切格式回复结构化简报：

```
SESSION LOADED: [实际解析的文件路径]
════════════════════════════════════════════════

PROJECT: [project name / topic from file]

WHAT WE'RE BUILDING:
[用您自己的话总结 2-3 句]

CURRENT STATE:
PASS: Working: [count] items confirmed
 In Progress: [list files that are in progress]
 Not Started: [list planned but untouched]

WHAT NOT TO RETRY:
[列出每个失败的方法及其原因——这是关键]

OPEN QUESTIONS / BLOCKERS:
[列出任何阻碍或未回答的问题]

NEXT STEP:
[如果文件中定义了确切的下一步]
[如果未定义："未定义下一步——建议在开始之前一起查看 'What Has NOT Been Tried Yet'"]

════════════════════════════════════════════════
Ready to continue. What would you like to do?
```

### 步骤 4: 等待用户

不要自动开始工作。不要触碰任何文件。等待用户说什么。

如果会话文件中明确定义了下一步，并且用户说"继续"或"是"或类似的——继续执行该确切的下一步。

如果未定义下一步——询问用户从哪里开始，并可选择从 "What Has NOT Been Tried Yet" 部分建议一种方法。

---

## 边缘情况

**同一日期的多个会话**（`2024-01-15-session.tmp`、`2024-01-15-abc123de-session.tmp`）：
加载该日期最近修改的匹配文件，无论它使用旧的无 ID 格式还是当前的短 ID 格式。

**会话文件引用的文件不再存在：**
在简报中注明——"警告：会话中引用的 `path/to/file.ts` 在磁盘上未找到。"

**会话文件来自 7 天多前：**
注明差距——"警告：此会话来自 N 天前（阈值：7 天）。情况可能已发生变化。"——然后正常继续。

**用户直接提供文件路径（例如，从队友转发）：**
阅读并遵循相同的简报流程——格式相同，无论来源如何。

**会话文件为空或格式错误：**
报告："找到会话文件但似乎为空或无法读取。您可能需要使用 /save-session 创建一个新的。"

---

## 示例输出

```
SESSION LOADED: /Users/you/.claude/session-data/2024-01-15-abc123de-session.tmp
════════════════════════════════════════════════

PROJECT: my-app — JWT Authentication

WHAT WE'RE BUILDING:
User authentication with JWT tokens stored in httpOnly cookies.
Register and login endpoints are partially done. Route protection
via middleware hasn't been started yet.

CURRENT STATE:
PASS: Working: 3 items (register endpoint, JWT generation, password hashing)
 In Progress: app/api/auth/login/route.ts (token works, cookie not set yet)
 Not Started: middleware.ts, app/login/page.tsx

WHAT NOT TO RETRY:
FAIL: Next-Auth — conflicts with custom Prisma adapter, threw adapter error on every request
FAIL: localStorage for JWT — causes SSR hydration mismatch, incompatible with Next.js

OPEN QUESTIONS / BLOCKERS:
- Does cookies().set() work inside a Route Handler or only Server Actions?

NEXT STEP:
In app/api/auth/login/route.ts — set the JWT as an httpOnly cookie using
cookies().set('token', jwt, { httpOnly: true, secure: true, sameSite: 'strict' })
then test with Postman for a Set-Cookie header in the response.

════════════════════════════════════════════════
Ready to continue. What would you like to do?
```

---

## 注意事项

- 加载时不要修改会话文件——这是一份只读的历史记录
- 简报格式是固定的——即使部分为空也不要跳过
- "What Not To Retry" 必须始终显示，即使只是说"无"——它太重要了不能遗漏
- 恢复后，用户可能希望在新的会话结束时再次运行 `/save-session` 以创建新的带日期的文件
