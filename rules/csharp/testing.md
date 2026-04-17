---
paths:
  - "**/*.cs"
  - "**/*.csx"
  - "**/*.csproj"
---
# C# 测试

> 本文件通过 C# 特定内容扩展了 [common/testing.md](../common/testing.md)。

## 测试框架

- 单元和集成测试首选 **xUnit**
- 使用 **FluentAssertions** 获得可读的断言
- 使用 **Moq** 或 **NSubstitute** 来 mock 依赖
- 集成测试需要真实基础设施时使用 **Testcontainers**

## 测试组织

- 在 `tests/` 下镜像 `src/` 结构
- 清晰分离单元、集成和端到端覆盖
- 按行为命名测试，而非按实现细节

```csharp
public sealed class OrderServiceTests
{
    [Fact]
    public async Task FindByIdAsync_ReturnsOrder_WhenOrderExists()
    {
        // Arrange
        // Act
        // Assert
    }
}
```

## ASP.NET Core 集成测试

- 使用 `WebApplicationFactory<TEntryPoint>` 进行 API 集成覆盖
- 通过 HTTP 测试认证、验证和序列化，而非绕过中间件

## 覆盖率

- 目标 80%+ 行覆盖率
- 专注于领域逻辑、验证、认证和失败路径
- 在 CI 中运行 `dotnet test`，在可用时启用覆盖率收集
