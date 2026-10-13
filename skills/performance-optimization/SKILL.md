---
name: performance-optimization
description: Optimizes application performance across frontend, backend, queries, and databases. Use when performance requirements exist, when you suspect performance regressions, when Core Web Vitals or load times need improvement, when N+1 query patterns need fixing, or when profiling reveals bottlenecks.
---

# 性能优化

## Overview


优化前的计量。没有计量的绩效工作是猜测 — — 而猜测导致过早优化,从而增加复杂性,而不会改善问题。概况首先找出实际的瓶颈,加以修复,再次测量。只优化哪些计量能证明问题。

## When to Use


- 性能要求见 (装载时间预算、反应时间)
- 用户或监测报告行为缓慢
- Core Web Vitals 分数低于阈值
-  你怀疑一个变化带来了回归
- 处理大数据集或高流量的建筑特征

** 不使用时 :**  在有问题的证据之前不要优化。 过早优化会增加复杂程度, 成本比它所获得的性能还要高 。

## Core Web Vitals 目标

| 度量衡 | 不错 | 需要改进 | 穷 |
|--------|------|-------------------|------|
| **LCP 常规程序** (内容最丰富的绘画) |  • 2.5s |                                                              |  > 4.0s (单位:千兆赫) |
| **INP 统计** (与下一张画的交互) |                                                                        |  ⁇  500ms(500ms) |  > 500毫秒( X) |
| **CLS 密码** (累积布局移动) | ≤ 0.1 | ≤ 0.25 | > 0.25 |

## 优化工作流程

```
1. MEASURE  → Establish baseline with real data
2. IDENTIFY → Find the actual bottleneck (not assumed)
3. FIX      → Address the specific bottleneck
4. VERIFY   → Measure again; keep or revert
5. GUARD    → Add monitoring or tests to prevent regression
```

### 步骤1:措施

两种相辅相成的办法——使用两种办法:

- **合成 (灯塔 DevTools 性能标签):** 控制条件,可复制。 CI 发现回归和孤立具体问题。
- **卢姆 (网络维图库, CRUX):** 真实条件下的真实用户数据。 需要验证此修正是否实际上改善了用户体验 。

** 前端 :**
```bash
# Synthetic: Lighthouse in Chrome DevTools (or CI)
# Chrome DevTools → Performance tab → Record
# Chrome DevTools MCP → Performance trace

# RUM: Web Vitals library in code
import { onLCP, onINP, onCLS } from 'web-vitals';

onLCP(console.log);
onINP(console.log);
onCLS(console.log);
```

**后端 :**
```bash
# Response time logging
# Application Performance Monitoring (APM)
# Database query logging with timing

# Simple timing
console.time('db-query');
const result = await db.query(...);
console.timeEnd('db-query');
```

### 在哪里开始测量

 用症状来决定先测量什么:

```
What is slow?
├── First page load
│   ├── Large bundle? --> Measure bundle size, check code splitting
│   ├── Slow server response? --> Measure TTFB in DevTools Network waterfall
│   │   ├── DNS long? --> Add dns-prefetch / preconnect for known origins
│   │   ├── TCP/TLS long? --> Enable HTTP/2, check edge deployment, keep-alive
│   │   └── Waiting (server) long? --> Profile backend, check queries and caching
│   └── Render-blocking resources? --> Check network waterfall for CSS/JS blocking
├── Interaction feels sluggish
│   ├── UI freezes on click? --> Profile main thread, look for long tasks (>50ms)
│   ├── Form input lag? --> Check re-renders, controlled component overhead
│   └── Animation jank? --> Check layout thrashing, forced reflows
├── Page after navigation
│   ├── Data loading? --> Measure API response times, check for waterfalls
│   └── Client rendering? --> Profile component render time, check for N+1 fetches
└── Backend / API
    ├── Single endpoint slow? --> Profile database queries, check indexes
    ├── All endpoints slow? --> Check connection pool, memory, CPU
    └── Intermittent slowness? --> Check for lock contention, GC pauses, external deps
```

### 步骤2: 识别 Bottleneck

 按类别分列的常见瓶颈:

** 前端 :**

| 症状 | 可能的原因是 | 调查 |
|---------|-------------|---------------|
| 慢 LCP 缓冲 | 大型图像、 渲染屏蔽资源、 慢服务器 | 检查网络瀑布, 图像大小 |
| 高级 CLS |  无维度的图像、 晚装内容、 字体变化 | 检查布局变换属性 |
| 贫穷的INP | 重型 JavaScript  主线程, 大型 DOM 更新 |  在性能跟踪中检查长任务 |
| 慢速初始负载 | 大型捆绑, 许多网络请求 | 检查捆绑大小, 代码分割 |

**后端 :**

| 症状 | 可能的原因是 | 调查 |
|---------|-------------|---------------|
| 慢点 API 答复 | N+1 查询,缺失索引,未优化查询 | 检查数据库查询日志 |
| 内存增长 | 泄漏的参考文献、无限制的缓存、大型有效载荷 | 高压快照分析 |
| CPU 标记 | 同步重度计算, regex 回溯跟踪 | CPU 剖析 |
| 高纬度 | 缺少缓存、 冗余计算、 网络跳动 |  通过堆栈跟踪请求 |

### 步骤 3: 修整瓶装

修补第2步确认的一件事,别无其他。下面的反标是通常的嫌疑犯;每个条目给出规则和签名来识别它,并与一个工作定律联系起来。 [references/optimization-patterns.md](references/optimization-patterns.md)打开你需要的,当你到达代码, 而不是之前。

**后端**

- **N+1查询.**  每行一个查询是最常见的后端瓶颈。在同一查询中获取关系 (join/include) 而不是在循环中。 [ 图案](references/optimization-patterns.md#n1-queries-backend).
- ** 未绑定的数据正在获取 。**  每个列表的终点都具有限制和稳定的顺序。 [ 图案](references/optimization-patterns.md#unbounded-data-fetching).
- **忽略其索引的查询。** "添加索引"是猜测; `EXPLAIN ANALYZE`  是测量。 A `Seq Scan`  其中,你期望一个索引,a `rows=` 以数量级和 `Sort` 扫描上方的节点, 每个调用不同的修正; 坏 `rows=` 估计意味着陈旧的统计数据,所以运行 `ANALYZE` 而不是添加索引。查询形状的索引 (平等列,然后是范围或排序列)。一个简单的索引将无助于查询低选择性主导值 (部分指数服务于稀有值),主通配符 (需要三格或全文),或应用到列上的函数 (索引表达式,如 `WHERE lower(email) = ?`),每一份指数税都写着。之后重新运行计划;一个没有改变的指数是反常的。 [ 图案](references/optimization-patterns.md#queries-that-ignore-their-index).
- **连接池已耗尽 。** 签署: * 每张* 结束点会同时减慢, 等待连接而非执行的时间会花在时间上, 数据库显示的会话大多是闲置的。 每个进程一个集合, 大小如此 。 `instances × max` 保留在数据库的连接上限下 。 大点不快; 它将队列迁移到难以看到的数据库中 。 以未绑定的实例计数 ( 无服务器, 自动缩放),通过代理服务器的多功能 (pgbouncer, RDS 代理服务器) 而不是提高 `max`. [ 图案](references/optimization-patterns.md#connection-pool-exhaustion).
- **失踪的棺材。** 缓存生成和读取的昂贵内容要比它改变的多得多; 缓存一个已经快的查询会添加一个网络 hop 和一个 staleness bug 以换取一无所有。 故意选择图层 (正在处理中, 共享, CDN)。每个修改响应的输入都属于密钥 (租户、地籍、权限、特征旗帜): 省略查看器的密钥是用户的数据如何被服务到另一个用户。 选择一个无效策略 (TTL, 事件或标签, 版本密钥) 并明确显示可接受的 Staleity 窗口。 将热键对准 印章 : 在一个请求重算时执行 stale , 或 将同时忽略在单个飞行中的承诺之后。 永远不要缓存必须新鲜的东西 。 (余额、权限、结账时的库存). [ 图案](references/optimization-patterns.md#missing-caching-backend); 请求组合、 写入策略和阴性缓存 `../../references/performance-checklist.md`.

** 前端 :**

- **缺少图像优化 。**  每个图像都声明 `width` 财务报告和财务报告 `height` (CLS 密码)。 LCP 图像得到 `fetchpriority="high"`,现代格式 (AVIF, 韦伯P) 结束 `<picture>`,以及 `srcset`/`sizes` 用于解析切换; 双面下图像获取 `loading="lazy"` 财务报告和财务报告 `decoding="async"`. [ 图案](references/optimization-patterns.md#missing-image-optimization-frontend).
- **没有必要的重新交代。**  在渲染中创建的物体或函数是每次给每个接收它的孩子的新的参考。 同步常数; 保留 `React.memo` 财务报告和财务报告 `useMemo`  由于过度使用是它自己的成本,所以简介节目很昂贵。 [ 图案](references/optimization-patterns.md#unnecessary-re-renders-react).
- ** 大块的捆绑尺寸。** 现代捆绑商树震无害环境管理将进口单独命名;实际收益是路由级代码拆分和懒惰重载,很少使用 `Suspense`。在修改导入样式之前,先设置配置 。 [ 图案](references/optimization-patterns.md#large-bundle-size).

### 步骤4:验证 (保存或还原)

固定是假设,直到你重新测量。这个步骤决定它是否存活下来。

**重新测量您测量基线的方式 :** 命令、条件和固定预算相同 (壁点、样本计数或请求计数) 在冷藏库上提取的基线 相对于热藏库的结果 而不是你的零用钱

**一次改变一件事。** 三次优化一起着陆产生一个数字,你无法归属它。如果它们必须一起航行,首先对每个数字进行隔离测量。

**击败噪音,不只是恶毒。** 重复测量并比较三角洲与运行中的差异。 ±5% 差异内3%的增益不是增益, 而是不同的样本 。

 然后严格地决定:

|  结果与基线 | 行动 |
|---|---|
| 超过阈值, 测试绿色 | **留着** 提交 before/after 消息中的数字。 |
|  在噪声中 ( 无可衡量变化) | **复还.** |
| 更惨 | **复还.** |
| 改进了,但测试红了 | **复还.** 戴着赢家衣服的倒退 |

**"Neutral"是一个回归,而不是保持.**  这就是步骤团队跳过: 修改已经写好了, 丢掉它会觉得很浪费, 所以它降落时没有测量, 代码库会增加复杂度, 从未买过任何东西。 代码保存, 永远保存。 让它自己支付。

**正确性是衡量标准的关键。** 套房是绿色的 *财务报告和财务报告* 数字移动 。 “ 优化” , 它通过丢弃产品所需的工作而获胜 。 (跳过验证, 缓存一定新鲜的东西, 删除一个 `await`  那确是有载荷的,) 是一种倒退,而不是胜利。

#### 记录每次尝试,包括返回的尝试

重回的作品在gt历史中没有留下任何痕迹,这正是下季度再次尝试同样枯燥的想法的原因。保持一个简短的分类账,这样一个被丢弃的想法就会被丢弃:

| 思想 | 基线 − 结果 | 判决 |  为什么 |
|---|---|---|---|
| 纪念行组件 | INP 240ms − 235ms (百万吨) | 已归还 | 内部的噪音 (±15ms (百万吨))排行不是瓶颈。 |
| 虚拟列表 | INP 240 ms − 90 ms (英语). | 保留 | 长期的任务从追踪。 |
| 预连接到 API 来源 | LCP 2.8s → 2.8s 高温 | 已归还 | 已经是同一起源的了 |

公关说明中的一节或 `PERF.md` 关键是下一个人 (或下一个代理)  在提出实验之前读它, 并且不会重新运行一个已经失败的实验。

### 步骤5:防止倒退

保护用户实际感受到的度量,而不是每个可用的数字。
相同的LCP, INP, p95 耐久性, 或者其他主要度量法, 说明修复的合理性.

 当表面为用户化时使用两个互补的层 :

- **合成 CI 门 :** 与 a 合并前抓取可复制回归
  重复的噪音测量或比较a median/trend 这么说吧
  普通的运行间差异不会将闸门变成片面检查。
- **实地监测:**  关于RUM数据中有意义的p75运动的警告。使用
  归属 `web-vitals` 用于查找原因的数据; 处理 CRUX 滚动窗口
  以示确认,而非立即警告。

警卫开火时,请回到第1步,并先确定一个新的基准线
提出另一个解决方案。

**制定预算并加以执行:**

```
JavaScript bundle: < 200KB gzipped (initial load)
CSS: < 50KB gzipped
Images: < 200KB per image (above the fold)
Fonts: < 100KB total
API response time: < 200ms (p95)
Time to Interactive: < 3.5s on 4G
Lighthouse Performance score: ≥ 90
```

**执行中 CI:**
```bash
# Bundle size check
npx bundlesize --config bundlesize.config.json

# Lighthouse CI
npx lhci autorun
```

## 另见

详细的性能核对表、优化命令和反模式参考,见 `../../references/performance-checklist.md`.


## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "我们晚点再优化" | 性能债务复合物 现在解决明显的反标 推迟微优化 |
| "我的机器开得很快" |  您的机器不是用户的,关于代表性硬件和网络的简介. |
| "这种优化是显而易见的" |  如果你没有测量,你不知道。 |
| "用户不会注意到100米" | 研究显示 100ms 延迟影响转换率 。 用户注意到的比你想象的要多 。 |
| "框架处理性能" | 框架可以防止某些问题,但不能修正N+1查询或超大小的捆绑. |
| "查询缓慢,添加索引" |  先读计划。 指数可能已经存在并且无法使用, 每一份指数税都会写到永远 。 |
| "只是缓存它" | 缓存一个已经很便宜的呼叫 买不到任何东西, 并添加一个 staleness bug。 缓存什么是昂贵的 *财务报告和财务报告* 重读远不止写. |
| "把池子拉大,我们没有联系了" |  一个大于数据库的池可以将队列移动到不太可见的地方。 找到哪些连接 。 |
| "没有多大帮助,但并不疼" | 中立的改变是一种倒退,你永远支付维持费,却一无所获。 |
| "我们已经写好了,还是留着吧" | Sunk成本,测量不考虑变化需要多长时间才能写出来. |
| "进步是显而易见的,不需要重新衡量" |  那么,重新测量是便宜的,并证明这一点。未计量的赢家就是中立的复杂程度。 |

## Red Flags


- 优化而不提供剖析数据以说明理由
- 数据获取中的 N+1 查询模式
-  在未制定查询计划之前和之后添加一个索引以证明其合理性
- 缓存密钥,该密钥省略了回复所依赖的输入 (租户、当地人、观众)
-  一个缓存, 没有显示 Staleness 窗口, 没有无效策略
-  为应付耗尽而提高的连接池大小,但没有找到哪些连接
- 列出没有页标的结束点
-  无尺寸、懒惰加载或应答大小的图像
- 组装大小未经过审查而增长
- 生产中无性能监测
- `React.memo` 财务报告和财务报告 `useMemo` 到处都是 (过量使用和过量使用一样糟糕)
- 保持优化,而不重新计量,以证明这些优化的合理性
- 多个优化组合到一个度量中, 所以无法归属于单个变化 。
- 需要更改、跳过或删除测试的“ 赢 ”
- 同样的失败优化尝试不止一次 因为没有人记录第一次尝试

## Verification


 在与业绩有关的任何变化之后:

- [ ] 测量数据存在之前和之后 (具体数字)
- [ ] 以与基线相同的方式对结果进行了重新测量 (相同的命令,同样的条件)
- [ ] 改进超过运行中的差异,而不只是平均值
- [ ]  未超过基线的更改被恢复,没有保持中性
- [ ] 尝试被记录, 保存和还原 相同,所以一个死的想法 不再运行
- [ ] 查明并解决了具体的瓶颈问题
- [ ] Core Web Vitals  在“好”阈值内
- [ ] 行李箱的尺寸没有明显增加
- [ ]  新数据获取代码中没有 N+1 查询
- [ ] 任何新的索引都以前后的查询计划为理由,其写价也得到了考虑
- [ ] 任何新的缓存都说明它的关键所在和它如何沉淀
- [ ] 测量的用户成像度量有合成预算或实地监测器,可以检测回归
- [ ] 现有测试仍然通过 (优化没有破坏行为)
