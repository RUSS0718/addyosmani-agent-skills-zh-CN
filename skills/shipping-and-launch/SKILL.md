---
name: shipping-and-launch
description: Prepares production launches. Use when preparing to deploy to production, or when asking what needs to be in place before shipping. Use when you need a pre-launch checklist, when setting up monitoring, when planning a staged rollout, or when you need a rollback strategy.
---

# 运输和发射

## Overview


满怀信心的飞船。目标不仅仅是部署,而是安全部署,同时监测到位,准备退航计划,明确了解成功是什么样子。 每一次发射都应该是可逆的、可观察的和渐进的。

## When to Use


- 首次在生产中部署一个特性
- 向用户提供重大改变
- 迁移数据或基础设施
- 打开β或早期访问程序
- 任何有风险的部署 ( 他们全部)

## 降雨前核对表

### 代码质量

- [ ]  所有测试合格 (单位,集成,e2e)
- [ ]  在没有警告的情况下构建成功
- [ ] 林特和类型检查合格证
- [ ] 守则得到审查和核准
- [ ]  没有应在发射前解决的TODO评论意见
- [ ]  没有 `console.log` 调试生产代码中的语句
- [ ] 处理错误覆盖预期失败模式

### 警卫

- [ ] 密码或版本控制中没有秘密
- [ ] 生态系统的依赖性审计 (`npm audit`, `pip-audit`, `cargo audit`, ...) 显示没有重大或高度脆弱性。
- [ ]  所有用户端点的输入验证
- [ ] 认证和授权检查到位
- [ ] 配置安全信头 (CSP, HSTS 等 (中文(简体) ).)
- [ ]  对认证端点的费率限制
- [ ] CORS 配置到特定来源 ( 非通配符)

### 业绩

- [ ] Core Web Vitals  在“ 好” 阈值内
- [ ] 关键路径中无 N+1 查询
- [ ] 图像优化 (压缩、响应大小、懒惰加载)
- [ ] 预算范围内的套装大小
- [ ] 数据库查询有适当的索引
- [ ]  为静态资产和重复查询配置的缓存

### 无障碍

- [ ]  所有交互式元素的键盘导航工作
- [ ] 屏幕阅读器可以传送页面内容和结构
- [ ] 颜色对比符合 WCAG 2.1 AA (4.5:1 案文)
- [ ] 模式和动态内容的焦点管理正确
- [ ] 错误信息是描述性的, 并与窗体字段相关
- [ ]  在轴心或灯塔没有无障碍警告

### 基础设施

- [ ] 生产中的环境变量
- [ ] 应用的数据库迁移 (或准备适用)
- [ ] DNS 和 SSL 已配置
- [ ]  为静态资产配置的 CDN
- [ ] 已配置日志和错误报告
- [ ] 健康检查终点已存在并响应

### 文档

- [ ] 以任何新设置要求更新 README
- [ ] API 文档当前
- [ ]  为任何建筑决定撰写的 ADRs
- [ ] 更新更改日志
- [ ] 更新用户文件 (适用时)

## 特色旗舰战略

船身在地物旗下 与释放分离部署:

```typescript
// Feature flag check
const flags = await getFeatureFlags(userId);

if (flags.taskSharing) {
  // New feature: task sharing
  return <TaskSharingPanel task={task} />;
}

// Default: existing behavior
return null;
```

**特性旗生命周期 :**

```
1. DEPLOY with flag OFF     → Code is in production but inactive
2. ENABLE for team/beta     → Internal testing in production environment
3. GRADUAL ROLLOUT          → 5% → 25% → 50% → 100% of users
4. MONITOR at each stage    → Watch error rates, performance, user feedback
5. CLEAN UP                 → Remove flag and dead code path after full rollout
```

**规则:**
-  每个地物旗都有主人和过期日期
-  在全面推出后两周内清理旗子
-  不要筑巢图案旗 (创建指数组合)
- 测试两个旗国 (打开或关闭) 输入 CI

## 分阶段推出

### 推出顺序

```
1. DEPLOY to staging
   └── Full test suite in staging environment
   └── Manual smoke test of critical flows

2. DEPLOY to production (feature flag OFF)
   └── Verify deployment succeeded (health check)
   └── Check error monitoring (no new errors)

3. ENABLE for team (flag ON for internal users)
   └── Team uses the feature in production
   └── 24-hour monitoring window

4. CANARY rollout (flag ON for 5% of users)
   └── Monitor error rates, latency, user behavior
   └── Compare metrics: canary vs. baseline
   └── 24-48 hour monitoring window
   └── Advance only if all thresholds pass (see table below)

5. GRADUAL increase (25% -> 50% -> 100%)
   └── Same monitoring at each step
   └── Ability to roll back to previous percentage at any point

6. FULL rollout (flag ON for all users)
   └── Monitor for 1 week
   └── Clean up feature flag
```

### 推出决定阈值

使用这些阈值来决定是否在每个阶段推进、持有或回滚:

| 度量衡 | 预付款 (绿色) | 坚持和调查 (黄色) | 滚回去 (红色) |
|--------|-----------------|-------------------------------|-----------------|
| 错误率 | 不超过基准量的10% | 超过基准10-100% |  >2x 基线 |
| P95 延迟 | 不超过基准量的20% | 超过基准20%-50% | 大于基准量50% |
| 客户 JS 错误 |  无新错误类型 |  新错误发生于 <0.1% of sessions | New errors at >0.1%的届会 |
| 商业衡量标准 | 中立或正数 | 缩写 <5% (may be noise) | Decline >5% |

### 何时退后

立即退后,如果:
- 错误率增加超过2x基线
- P95 延迟增加50%以上
- 用户报告的问题激增
- 发现的数据完整性问题
- 发现安全弱点

## 监测和可观察性

### 需要监视什么

```
Application metrics:
├── Error rate (total and by endpoint)
├── Response time (p50, p95, p99)
├── Request volume
├── Active users
└── Key business metrics (conversion, engagement)

Infrastructure metrics:
├── CPU and memory utilization
├── Database connection pool usage
├── Disk space
├── Network latency
└── Queue depth (if applicable)

Client metrics:
├── Core Web Vitals (LCP, INP, CLS)
├── JavaScript errors
├── API error rates from client perspective
└── Page load time
```

### 报告错误

```typescript
// Set up error boundary with reporting
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    // Report to error tracking service
    reportError(error, {
      componentStack: info.componentStack,
      userId: getCurrentUser()?.id,
      page: window.location.pathname,
    });
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback onRetry={() => this.setState({ hasError: false })} />;
    }
    return this.props.children;
  }
}

// Server-side error reporting
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  reportError(err, {
    method: req.method,
    url: req.url,
    userId: req.user?.id,
  });

  // Don't expose internals to users
  res.status(500).json({
    error: { code: 'INTERNAL_ERROR', message: 'Something went wrong' },
  });
});
```

### 故障后核查

 在发射后的第一个小时:

```
1. Check health endpoint returns 200
2. Check error monitoring dashboard (no new error types)
3. Check latency dashboard (no regression)
4. Test the critical user flow manually
5. Verify logs are flowing and readable
6. Confirm rollback mechanism works (dry run if possible)
```

## 预算发布门出错

 您服务的误差预算—— 您的 SLO 允许失败的请求或时间的一小部分—— 决定它是否安全可以运送。 使用它作为客观的门而不是谈判 :

```
Budget remaining > 20%  →  Ship normally; monitor closely
Budget remaining 0–20%  →  Slow rollouts only; no high-risk changes
Budget exhausted        →  Freeze feature work; focus entirely on reliability
Budget resets           →  Resume normal pace; bake in the fix that recovered it
```

一只金丝雀燃烧率很高 (消耗预算的速度高于基线速度) 是一个 **拿着**  在上方的推出阈值表中的信号——将其与较高的误差率相同。

##  回滚战略

 每一次部署都需要一个回滚计划,然后才进行:

```markdown
## Rollback Plan for [Feature/Release]

### Trigger Conditions
- Error rate > 2x baseline
- P95 latency > [X]ms
- User reports of [specific issue]

### Rollback Steps
1. Disable feature flag (if applicable)
   OR
1. Deploy previous version: `git revert <commit> && git push`
2. Verify rollback: health check, error monitoring
3. Communicate: notify team of rollback

### Database Considerations
- Migration [X] has a rollback: <verified command or runbook link>
- Data inserted by new feature: [preserved / cleaned up]

### Time to Rollback
- Feature flag: < 1 minute
- Redeploy previous version: < 5 minutes
- Database rollback: < 15 minutes
```
## 另见

-  关于整个项目的定义,即每一改动都必须在本清单之前予以澄清,见 `../../references/definition-of-done.md`
-  关于发射前的安全检查,见 `../../references/security-checklist.md`
-  关于发射前的性能核对表,见 `../../references/performance-checklist.md`
-  关于发射前的无障碍核查,见 `../../references/accessibility-checklist.md`
-  关于警报规则和SLO带阈值,参见: `observability-and-instrumentation`

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "它的工作在中转, 它会工作在生产" | 生产有不同的数据、流量模式和边缘病例。部署后监测器。 |
| "我们不需要特别的旗帜" |  每个特性都从杀机开关中获益,即使是"简单"的改变也能打破事物. |
| "监视是头顶的" |  没有监控意味着你从用户投诉中而不是从仪表板中发现问题. |
| "我们以后会增加监控" |  在发射前添加,不能调试无法看到的东西. |
| "回滚就是承认失败" | 退航是负责任的工程 运输失败是破败的特征 |
| "错误率看起来不错,让我们继续运输" |  请检查燃烧率, 而不仅仅是当前错误率。 即使单个阈值是绿色的, 消耗预算的速度也比基线快 。 |

## Red Flags


- 部署时没有退缩计划
- 生产中没有监测或错误报告
-  大爆炸释放 (万物一时,无中转)
-  无过期或所有者的特征旗帜
- 头一个小时都没人监视部署
- 生产环境配置由内存而不是代码完成
- "现在是星期五下午,我们出发吧"
- 预算错误已用尽, 但特性工作仍在进行中

## Verification


部署前:

- [ ] 完成发射前核对表 ( 所有部分均为绿色)
- [ ] 配置特性旗 (适用时)
- [ ] 已记录回滚计划
- [ ] 安装监测仪表板
- [ ] 已通知部署小组

部署后:

- [ ] 健康检查结果 200
- [ ] 错误率是正常的
- [ ] 空闲是正常的
- [ ] 关键用户流动工程
- [ ] 日志在流动
- [ ] 已进行回滚测试或核实已就绪

 每一次发货服务:

- [ ] 现行预算政策出错:当预算下降20%以下,当预算用尽时,知道应采取何种行动
