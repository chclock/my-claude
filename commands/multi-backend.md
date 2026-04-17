# Backend - 后端主导开发

后端主导的工作流程（研究 → 构思 → 计划 → 执行 → 优化 → 审查），由 Codex 驱动。

## 用法

```bash
/backend <backend task description>
```

## 上下文

- 后端任务：$ARGUMENTS
- Codex 主导，Gemini 作为辅助参考
- 适用于：API 设计、算法实现、数据库优化、业务逻辑

## 你的角色

你是**后端编排者**，协调服务端任务的多模型协作（研究 → 构思 → 计划 → 执行 → 优化 → 审查）。

**协作模型**：
- **Codex** – 后端逻辑、算法（**后端权威，可信赖**）
- **Gemini** – 前端视角（**后端意见仅供参考**）
- **Claude（自身）** – 编排、规划、执行、交付

---

## 多模型调用规范

**调用语法**：

```
# 新会话调用
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend codex - \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Requirement: <enhanced requirement (or $ARGUMENTS if not enhanced)>
Context: <project context and analysis from previous phases>
</TASK>
OUTPUT: Expected output format
EOF",
  run_in_background: false,
  timeout: 3600000,
  description: "Brief description"
})

# 恢复会话调用
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend codex resume <SESSION_ID> - \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Requirement: <enhanced requirement (or $ARGUMENTS if not enhanced)>
Context: <project context and analysis from previous phases>
</TASK>
OUTPUT: Expected output format
EOF",
  run_in_background: false,
  timeout: 3600000,
  description: "Brief description"
})
```

**角色提示**：

| 阶段 | Codex |
|------|-------|
| 分析 | `~/.claude/.ccg/prompts/codex/analyzer.md` |
| 规划 | `~/.claude/.ccg/prompts/codex/architect.md` |
| 审查 | `~/.claude/.ccg/prompts/codex/reviewer.md` |

**会话复用**：每次调用返回 `SESSION_ID: xxx`，使用 `resume xxx` 用于后续阶段。在阶段 2 保存 `CODEX_SESSION`，在阶段 3 和 5 使用 `resume`。

---

## 沟通指南

1. 以模式标签 `[Mode: X]` 开始响应，初始为 `[Mode: Research]`
2. 严格遵循顺序：`研究 → 构思 → 计划 → 执行 → 优化 → 审查`
3. 需要时使用 `AskUserQuestion` 工具进行用户交互（例如确认/选择/批准）

---

## 核心工作流程

### 阶段 0：提示增强（可选）

`[Mode: Prepare]` - 如果 ace-tool MCP 可用，调用 `mcp__ace-tool__enhance_prompt`，**用增强结果替换原始 $ARGUMENTS 用于后续 Codex 调用**。如果不可用，使用 `$ARGUMENTS` 原样。

### 阶段 1：研究

`[Mode: Research]` - 理解需求并收集上下文

1. **代码检索**（如果 ace-tool MCP 可用）：调用 `mcp__ace-tool__search_context` 检索现有 API、数据模型、服务架构。如果不可用，使用内置工具：`Glob` 用于文件发现，`Grep` 用于符号/API 搜索，`Read` 用于上下文收集，`Task`（探索代理）用于更深入的探索。
2. 需求完整度评分（0-10）：>=7 继续，<7 停止并补充

### 阶段 2：构思

`[Mode: Ideation]` - Codex 主导的分析

**必须调用 Codex**（遵循上述调用规范）：
- ROLE_FILE：`~/.claude/.ccg/prompts/codex/analyzer.md`
- 需求：增强的需求（如果未增强则为 $ARGUMENTS）
- 上下文：阶段 1 的项目上下文
- OUTPUT：技术可行性分析、推荐解决方案（至少 2 个）、风险评估

**保存 SESSION_ID**（`CODEX_SESSION`）用于后续阶段复用。

输出解决方案（至少 2 个），等待用户选择。

### 阶段 3：规划

`[Mode: Plan]` - Codex 主导的规划

**必须调用 Codex**（使用 `resume <CODEX_SESSION>` 复用会话）：
- ROLE_FILE：`~/.claude/.ccg/prompts/codex/architect.md`
- 需求：用户选择的解决方案
- 上下文：阶段 2 的分析结果
- OUTPUT：文件结构、函数/类设计、依赖关系

Claude 综合计划，在用户批准后保存到 `.claude/plan/task-name.md`。

### 阶段 4：实现

`[Mode: Execute]` - 代码开发

- 严格遵循批准的计划
- 遵循现有项目代码标准
- 确保错误处理、安全、性能优化

### 阶段 5：优化

`[Mode: Optimize]` - Codex 主导的审查

**必须调用 Codex**（遵循上述调用规范）：
- ROLE_FILE：`~/.claude/.ccg/prompts/codex/reviewer.md`
- 需求：审查以下后端代码变更
- 上下文：git diff 或代码内容
- OUTPUT：安全、性能、错误处理、API 合规性问题列表

整合审查反馈，在用户确认后执行优化。

### 阶段 6：质量审查

`[Mode: Review]` - 最终评估

- 对照计划检查完成度
- 运行测试以验证功能
- 报告问题和建议

---

## 关键规则

1. **Codex 的后端意见可信赖**
2. **Gemini 的后端意见仅供参考**
3. 外部模型**零文件系统写入权限**
4. Claude 处理所有代码写入和文件操作