---
paths:
  - "**/*.cs"
  - "**/*.csx"
---
# C# 编码风格

> 本文件通过 C# 特定内容扩展了 [common/coding-style.md](../common/coding-style.md)。

## 标准

- 遵循当前 .NET 约定并启用可空引用类型
- 在公共和内部 API 上首选显式访问修饰符
- 保持文件与其定义的主要类型对齐

## 类型和模型

- 首选不可变值类模型的 `record` 或 `record struct`
- 对具有身份和生命周期的实体使用 `class`
- 使用 `interface` 表示服务边界和抽象
- 避免在应用代码中使用 `dynamic`；首选泛型或显式模型

```csharp
public sealed record UserDto(Guid Id, string Email);

public interface IUserRepository
{
    Task<UserDto?> FindByIdAsync(Guid id, CancellationToken cancellationToken);
}
```

## 不可变性

- 首选 `init` 访问器、构造函数参数和不可变集合处理共享状态
- 在生成更新状态时不要就地变更输入模型

```csharp
public sealed record UserProfile(string Name, string Email);

public static UserProfile Rename(UserProfile profile, string name) =>
    profile with { Name = name };
```

## 异步和错误处理

- 首选 `async`/`await`，而非 `.Result` 或 `.Wait()` 等阻塞调用
- 通过公共异步 API 传递 `CancellationToken`
- 抛出特定异常并使用结构化属性记录

```csharp
public async Task<Order> LoadOrderAsync(
    Guid orderId,
    CancellationToken cancellationToken)
{
    try
    {
        return await repository.FindAsync(orderId, cancellationToken)
            ?? throw new InvalidOperationException($"Order {orderId} was not found.");
    }
    catch (Exception ex)
    {
        logger.LogError(ex, "Failed to load order {OrderId}", orderId);
        throw;
    }
}
```

## 格式化

- 使用 `dotnet format` 进行格式化和分析器修复
- 保持 `using` 指令有序，移除未使用的导入
- 仅在保持可读性时使用表达式-bodied 成员
