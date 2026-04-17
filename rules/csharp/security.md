---
paths:
  - "**/*.cs"
  - "**/*.csx"
  - "**/*.csproj"
  - "**/appsettings*.json"
---
# C# 安全

> 本文件通过 C# 特定内容扩展了 [common/security.md](../common/security.md)。

## 密钥管理

- 绝不将 API 密钥、令牌或连接字符串硬编码到源代码中
- 使用环境变量、用户密钥进行本地开发，生产环境使用密钥管理器
- 保持 `appsettings.*.json` 不含真实凭证

```csharp
// 错误
const string ApiKey = "sk-live-123";

// 正确
var apiKey = builder.Configuration["OpenAI:ApiKey"]
    ?? throw new InvalidOperationException("OpenAI:ApiKey is not configured.");
```

## SQL 注入防护

- 始终使用 ADO.NET、Dapper 或 EF Core 的参数化查询
- 绝不将用户输入拼接到 SQL 字符串中
- 在使用动态查询组合前验证排序字段和过滤操作符

```csharp
const string sql = "SELECT * FROM Orders WHERE CustomerId = @customerId";
await connection.QueryAsync<Order>(sql, new { customerId });
```

## 输入验证

- 在应用边界验证 DTO
- 使用数据注解、FluentValidation 或显式 guard 子句
- 在运行业务逻辑前拒绝无效的模型状态

## 认证和授权

- 首选框架认证处理器而非自定义令牌解析
- 在端点或处理器边界强制执行授权策略
- 绝不记录原始令牌、密码或 PII

## 错误处理

- 返回安全的面向客户端的消息
- 记录服务器端详细异常及结构化上下文
- 不要在 API 响应中暴露堆栈跟踪、SQL 文本或文件系统路径

## 参考

有关更广泛的应用安全审查检查清单，请参阅 skill：`security-review`。
