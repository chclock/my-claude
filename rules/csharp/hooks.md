---
paths:
  - "**/*.cs"
  - "**/*.csx"
  - "**/*.csproj"
  - "**/*.sln"
  - "**/Directory.Build.props"
  - "**/Directory.Build.targets"
---
# C# Hooks

> 本文件通过 C# 特定内容扩展了 [common/hooks.md](../common/hooks.md)。

## PostToolUse Hooks

在 `~/.claude/settings.json` 中配置：

- **dotnet format**：自动格式化编辑的 C# 文件并应用分析器修复
- **dotnet build**：在编辑后验证解决方案或项目仍可编译
- **dotnet test --no-build**：在行为变更后重新运行最近的相关测试项目

## Stop Hooks

- 在包含广泛 C# 更改的会话结束前运行最终的 `dotnet build`
- 警告修改的 `appsettings*.json` 文件，以免提交秘密
