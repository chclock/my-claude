---
name: promote
description: Promote project-scoped instincts to global scope
command: true
---

# Promote 命令

将 instincts 从项目范围提升到 continuous-learning-v2 的全局范围。

## 实现方式

使用插件根路径运行 instinct CLI：

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" promote [instinct-id] [--force] [--dry-run]
```

或者如果未设置 `CLAUDE_PLUGIN_ROOT`（手动安装）：

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py promote [instinct-id] [--force] [--dry-run]
```

## 使用方法

```bash
/promote                      # 自动检测可提升的候选项
/promote --dry-run            # 预览自动提升候选项
/promote --force              # 无需确认即提升所有符合条件的候选项
/promote grep-before-edit     # 从当前项目提升某个特定的 instinct
```

## 执行步骤

1. 检测当前项目
2. 如果提供了 `instinct-id`，则仅提升该 instinct（如果存在于当前项目中）
3. 否则，查找跨项目候选项，符合以下条件：
   - 出现在至少 2 个项目中
   - 达到置信度阈值
4. 将提升的 instincts 写入 `~/.claude/homunculus/instincts/personal/`，设置 `scope: global`
