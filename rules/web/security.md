> 本文件通过 Web 特定的安全内容扩展了 [common/security.md](../common/security.md)。

# Web 安全规则

## 内容安全策略

始终配置生产 CSP。

### 基于 Nonce 的 CSP

对脚本使用每次请求的 nonce，而非 `'unsafe-inline'`。

```text
Content-Security-Policy:
  default-src 'self';
  script-src 'self' 'nonce-{RANDOM}' https://cdn.jsdelivr.net;
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  img-src 'self' data: https:;
  font-src 'self' https://fonts.gstatic.com;
  connect-src 'self' https://*.example.com;
  frame-src 'none';
  object-src 'none';
  base-uri 'self';
```

根据项目调整来源。不要不加修改地复制此块。

## XSS 防护

- 绝不注入未清理的 HTML
- 除非先清理，否则避免 `innerHTML` / `dangerouslySetInnerHTML`
- 转义动态模板值
- 绝对必要时使用经过审查的本地清理器清理用户 HTML

## 第三方脚本

- 异步加载
- 从 CDN 提供时使用 SRI
- 每季度审计
- 实际可行时优先自托管关键依赖

## HTTPS 和头部

```text
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

## 表单

- 状态变更表单需要 CSRF 保护
- 提交端点需要速率限制
- 客户端和服务器端都要验证
- 优先使用蜜罐或轻量级反滥用控制，而非默认使用繁重的 CAPTCHA
