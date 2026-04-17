> 本文件通过 Web 特定的性能内容扩展了 [common/performance.md](../common/performance.md)。

# Web 性能规则

## Core Web Vitals 目标

| 指标 | 目标 |
|--------|--------|
| LCP | < 2.5s |
| INP | < 200ms |
| CLS | < 0.1 |
| FCP | < 1.5s |
| TBT | < 200ms |

## 包预算

| 页面类型 | JS 预算（压缩后） | CSS 预算 |
|-----------|---------------------|------------|
| 落地页 | < 150kb | < 30kb |
| 应用页 | < 300kb | < 50kb |
| 微网站 | < 80kb | < 15kb |

## 加载策略

1. 在合理时内联关键首屏 CSS
2. 仅预加载 hero 图片和主要字体
3. 延迟非关键 CSS 或 JS
4. 动态导入重型库

```js
const gsapModule = await import('gsap');
const { ScrollTrigger } = await import('gsap/ScrollTrigger');
```

## 图片优化

- 明确的 `width` 和 `height`
- 仅对 hero 媒体使用 `loading="eager"` 加 `fetchpriority="high"`
- 对首屏以下资源使用 `loading="lazy"`
- 优先使用 AVIF 或 WebP 并有后备
- 绝不发送远大于渲染尺寸的源图片

## 字体加载

- 除非有明确例外，最多两种字体家族
- `font-display: swap`
- 尽可能子集化
- 仅预加载真正关键的 weight/style

## 动画性能

- 仅动画合成器友好属性
- 狭隘使用 `will-change`，用完后移除
- 简单过渡优先使用 CSS
- JS 动画使用 `requestAnimationFrame` 或成熟的动画库
- 避免滚动处理器抖动；使用 IntersectionObserver 或行为良好的库

## 性能检查清单

- [ ] 所有图片都有明确尺寸
- [ ] 无意外的渲染阻塞资源
- [ ] 动态内容无布局偏移
- [ ] 动画保持在合成器友好属性上
- [ ] 第三方脚本异步/延迟加载且仅在需要时加载
