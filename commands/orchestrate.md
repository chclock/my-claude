---
description: dmux-workflows 和 autonomous-agent-harness 的传统斜杠入口兼容层。推荐直接使用 skills。
---

# Orchestrate 命令（传统兼容层）

仅在仍在调用 `/orchestrate` 时使用此命令。主要的编排指南位于 `skills/dmux-workflows/SKILL.md` 和 `skills/autonomous-agent-harness/SKILL.md`。

## 标准入口

- 对于并行面板、工作树和多代理拆分，推荐使用 `dmux-workflows`。
- 对于长时间运行的循环、治理、调度和控制平面风格的执行，推荐使用 `autonomous-agent-harness`。
- 仅将此文件作为兼容性入口点保留。

## 参数

`$ARGUMENTS`

## 委托

请应用编排 skills，而不是在此维护第二个工作流程规范。
- 对于拆分/并行执行，从 `dmux-workflows` 开始。
- 当用户真正需要持久循环、治理或操作员层行为时，引入 `autonomous-agent-harness`。
- 保持交接结构化，但让 skills 定义维护的排序规则。

安全审查者：[summary]

### 修改的文件

[列出所有修改的文件]

### 测试结果

[测试通过/失败摘要]

### 安全状态

[安全发现]

### 建议

[发布 / 需要工作 / 阻塞]
```

## 并行执行

对于独立检查，并行运行代理：

```markdown
### 并行阶段
同时运行：
- code-reviewer（质量）
- security-reviewer（安全）
- architect（设计）

### 合并结果
将输出合并为单一报告
```

对于外部 tmux 面板工作进程和独立的 git 工作树，使用 `node scripts/orchestrate-worktrees.js plan.json --execute`。内置编排模式保持在进程内；辅助脚本用于长时间运行或跨 harness 的会话。

当工作进程需要查看主检出的脏文件或未跟踪的本地文件时，将 `seedPaths` 添加到计划文件中。ECC 在 `git worktree add` 之后仅将那些选定的路径覆盖到每个工作进程工作树中，这保持分支隔离同时仍暴露进行中的本地脚本、计划或文档。

```json
{
  "sessionName": "workflow-e2e",
  "seedPaths": [
    "scripts/orchestrate-worktrees.js",
    "scripts/lib/tmux-worktree-orchestrator.js",
    ".claude/plan/workflow-e2e-test.json"
  ],
  "workers": [
    { "name": "docs", "task": "Update orchestration docs." }
  ]
}
```

要导出现场 tmux/worktree 会话的控制平面快照，请运行：

```bash
node scripts/orchestration-status.js .claude/plan/workflow-visual-proof.json
```

快照包括会话活动、tmux 面板元数据、工作进程状态、目标、种子覆盖和最近的交接摘要（JSON 形式）。

## 操作员指挥中心交接

当工作流程跨越多个会话、工作树或 tmux 面板时，将控制平面块附加到最终交接：

```markdown
控制平面
-------------
会话：
- 活动会话 ID 或别名
- 每个活动工作进程的分支 + 工作树路径
- 适用的 tmux 面板或分离会话名称

差异：
- git 状态摘要
- 触摸文件的 git diff --stat
- 合并/冲突风险说明

批准：
- 待处理的用户批准
- 等待确认的阻塞步骤

遥测：
- 最后活动 timestamp 或空闲信号
- 预计 token 或成本偏差
- 钩子或审查者引发的策略事件
```

这使 planner、implementer、reviewer 和循环工作进程从操作员界面保持可读。

## 工作流程参数

$ARGUMENTS：
- `feature <description>` - 完整功能工作流程
- `bugfix <description>` - 缺陷修复工作流程
- `refactor <description>` - 重构工作流程
- `security <description>` - 安全审查工作流程
- `custom <agents> <description>` - 自定义代理序列

## 自定义工作流程示例

```
/orchestrate custom "architect,tdd-guide,code-reviewer" "Redesign caching layer"
```

## 提示

1. **对于复杂功能，先从 planner 开始**
2. **合并前始终包含 code-reviewer**
3. **对于认证/支付/PII 使用 security-reviewer**
4. **保持交接简洁** - 专注于下一个代理需要什么
5. **如需要，在代理之间运行验证**