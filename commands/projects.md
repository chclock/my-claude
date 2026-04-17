---
name: projects
description: List known projects and their instinct statistics
command: true
---

# Projects 命令

列出项目注册表条目以及 continuous-learning-v2 的每个项目 instinct/observation 计数。

## 实现

使用插件根路径运行 instinct CLI：

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" projects
```

或者如果未设置 `CLAUDE_PLUGIN_ROOT`（手动安装）：

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py projects
```

## 用法

```bash
/projects
```

## 做什么

1. 读取 `~/.claude/homunculus/projects.json`
2. 对于每个项目，显示：
   - 项目名称、id、根目录、远程仓库
   - 个人和继承的 instinct 计数
   - Observation 事件计数
   - 最后访问时间戳
3. 同时显示全局 instinct 总数