---
name: skill-health
description: 显示技能组合健康仪表板，包含图表和分析
command: true
---

# 技能健康仪表板

显示组合中所有技能的全面健康仪表板，包含成功率趋势线、失败模式聚类、待处理修订和版本历史。

## 实现

以仪表板模式运行技能健康 CLI：

```bash
ECC_ROOT="${CLAUDE_PLUGIN_ROOT:-$(node -e "var p=require('path'),f=require('fs'),h=require('os').homedir(),d=p.join(h,'.claude'),q=p.join('scripts','lib','utils.js');if(!f.existsSync(p.join(d,q))){try{var b=p.join(d,'plugins','cache','everything-claude-code');for(var o of f.readdirSync(b))for(var v of f.readdirSync(p.join(b,o))){var c=p.join(b,o,v);if(f.existsSync(p.join(c,q))){d=c;break}}}catch(x){}}console.log(d)")}"
node "$ECC_ROOT/scripts/skills-health.js" --dashboard
```

仅显示特定面板：

```bash
ECC_ROOT="${CLAUDE_PLUGIN_ROOT:-$(node -e "var p=require('path'),f=require('fs'),h=require('os').homedir(),d=p.join(h,'.claude'),q=p.join('scripts','lib','utils.js');if(!f.existsSync(p.join(d,q))){try{var b=p.join(d,'plugins','cache','everything-claude-code');for(var o of f.readdirSync(b))for(var v of f.readdirSync(p.join(b,o))){var c=p.join(b,o,v);if(f.existsSync(p.join(c,q))){d=c;break}}}catch(x){}}console.log(d)")}"
node "$ECC_ROOT/scripts/skills-health.js" --dashboard --panel failures
```

机器可读输出：

```bash
ECC_ROOT="${CLAUDE_PLUGIN_ROOT:-$(node -e "var p=require('path'),f=require('fs'),h=require('os').homedir(),d=p.join(h,'.claude'),q=p.join('scripts','lib','utils.js');if(!f.existsSync(p.join(d,q))){try{var b=p.join(d,'plugins','cache','everything-claude-code');for(var o of f.readdirSync(b))for(var v of f.readdirSync(p.join(b,o))){var c=p.join(b,o,v);if(f.existsSync(p.join(c,q))){d=c;break}}}catch(x){}}console.log(d)")}"
node "$ECC_ROOT/scripts/skills-health.js" --dashboard --json
```

## 用法

```
/skill-health                    # 完整仪表板视图
/skill-health --panel failures   # 仅失败聚类面板
/skill-health --json             # 机器可读的 JSON 输出
```

## 做什么

1. 使用 --dashboard 标志运行 skills-health.js 脚本
2. 向用户显示输出
3. 如果任何技能在衰退，高亮显示并建议运行 /evolve
4. 如果有待处理的修订，建议查看

## 面板

- **成功率（30天）** — 显示每个技能每日成功率的趋势线图表
- **失败模式** — 带水平条形图的聚类失败原因
- **待处理修订** — 等待审核的修订提案
- **版本历史** — 每个技能的版本快照时间线
