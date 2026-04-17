---
description: Comprehensive Go code review for idiomatic patterns, concurrency safety, error handling, and security. Invokes the go-reviewer agent.
---

# Go 代码审查

此命令调用 **go-reviewer** agent 进行全面的 Go 特定代码审查。

## 此命令的作用

1. **识别 Go 变更**：通过 `git diff` 查找修改的 `.go` 文件
2. **运行静态分析**：执行 `go vet`、`staticcheck` 和 `golangci-lint`
3. **安全扫描**：检查 SQL 注入、命令注入、竞态条件
4. **并发审查**：分析 goroutine 安全性、通道使用、互斥锁模式
5. **惯用 Go 检查**：验证代码是否符合 Go 约定和最佳实践
6. **生成报告**：按严重程度对问题进行分类

## 使用场景

在以下情况下使用 `/go-review`：
- 编写或修改 Go 代码之后
- 提交 Go 变更之前
- 审查包含 Go 代码的 pull requests
- 加入新的 Go 代码库时
- 学习惯用 Go 模式时

## 审查分类

### 严重（必须修复）
- SQL/命令注入漏洞
- 没有同步的竞态条件
- Goroutine 泄漏
- 硬编码凭证
- 不安全的指针使用
- 关键路径上被忽略的错误

### 高（应该修复）
- 缺少带 context 的错误包装
- 使用 panic 而非错误返回
- Context 未传播
- 未缓冲通道导致死锁
- 接口未满足错误
- 缺少互斥锁保护

### 中（考虑修复）
- 非惯用代码模式
- 导出缺少 godoc 注释
- 低效的字符串拼接
- Slice 未预分配
- 未使用表驱动测试

## 自动运行的检查

```bash
# 静态分析
go vet ./...

# 高级检查（如已安装）
staticcheck ./...
golangci-lint run

# 竞态检测
go build -race ./...

# 安全漏洞
govulncheck ./...
```

## 使用示例

```text
用户: /go-review

Agent:
# Go 代码审查报告

## 审查的文件
- internal/handler/user.go (已修改)
- internal/service/auth.go (已修改)

## 静态分析结果
✓ go vet: 无问题
✓ staticcheck: 无问题

## 发现的问题

[严重] 竞态条件
文件: internal/service/auth.go:45
问题: 共享 map 在没有同步的情况下访问
```go
var cache = map[string]*Session{}  // 并发访问！

func GetSession(id string) *Session {
    return cache[id]  // 竞态条件
}
```
修复: 使用 sync.RWMutex 或 sync.Map
```go
var (
    cache   = map[string]*Session{}
    cacheMu sync.RWMutex
)

func GetSession(id string) *Session {
    cacheMu.RLock()
    defer cacheMu.RUnlock()
    return cache[id]
}
```

[高] 缺少错误上下文
文件: internal/handler/user.go:28
问题: 返回的错误没有上下文
```go
return err  // 没有上下文
```
修复: 使用 context 包装
```go
return fmt.Errorf("get user %s: %w", userID, err)
```

## 总结
- 严重: 1
- 高: 1
- 中: 0

建议: 失败：阻止合并，直至严重问题修复
```

## 审批标准

| 状态 | 条件 |
|--------|-----------|
| 通过: 批准 | 无严重或高级问题 |
| 警告: 警告 | 只有中级问题（谨慎合并）|
| 失败: 阻止 | 发现严重或高级问题 |

## 与其他命令的集成

- 先使用 `/go-test` 确保测试通过
- 如果出现构建错误，使用 `/go-build`
- 提交前使用 `/go-review`
- 使用 `/code-review` 处理非 Go 特定的问题

## 相关

- Agent: `agents/go-reviewer.md`
- 技能: `skills/golang-patterns/`、`skills/golang-testing/`
