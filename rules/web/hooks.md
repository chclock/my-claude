> 本文件通过 Web 特定的 hook 建议扩展了 [common/hooks.md](../common/hooks.md)。

# Web Hooks

## 推荐的 PostToolUse Hooks

优先使用项目本地工具。不要将 hooks 连接到远程一次性包执行。

### 保存时格式化

在编辑后使用项目现有的格式化入口点：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "pnpm prettier --write \"$FILE_PATH\"",
        "description": "格式化编辑的前端文件"
      }
    ]
  }
}
```

当使用 repo 拥有的依赖时，通过 `yarn prettier` 或 `npm exec prettier --` 的等价本地命令也可以。

### Lint 检查

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "pnpm eslint --fix \"$FILE_PATH\"",
        "description": "在编辑的前端文件上运行 ESLint"
      }
    ]
  }
}
```

### 类型检查

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "pnpm tsc --noEmit --pretty false",
        "description": "前端编辑后进行类型检查"
      }
    ]
  }
}
```

### CSS Lint

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "pnpm stylelint --fix \"$FILE_PATH\"",
        "description": "Lint 编辑的样式表"
      }
    ]
  }
}
```

## PreToolUse Hooks

### 守护文件大小

从工具输入内容阻止过大写入，而非从可能尚不存在的文件：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "command": "node -e \"let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const c=i.tool_input?.content||'';const lines=c.split('\\n').length;if(lines>800){console.error('[Hook] BLOCKED: File exceeds 800 lines ('+lines+' lines)');console.error('[Hook] Split into smaller modules');process.exit(2)}console.log(d)})\"",
        "description": "阻止超过 800 行的写入"
      }
    ]
  }
}
```

## Stop Hooks

### 最终构建验证

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "pnpm build",
        "description": "在会话结束时验证生产构建"
      }
    ]
  }
}
```

## 顺序

推荐顺序：
1. 格式化
2. Lint
3. 类型检查
4. 构建验证
