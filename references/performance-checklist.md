# 性能核对表

用于网络应用性能的快速参考清单。 `performance-optimization` 技术。

##  目录

- [Core Web Vitals 目标](#core-web-vitals-targets)
- [TTFB 诊断](#ttfb-diagnosis)
- [ 前端检查列表](#frontend-checklist)
- [后端检查列表](#backend-checklist)
- [缓存策略](#caching-strategies)
- [测量命令](#measurement-commands)
- [常见反恐怖分子](#common-anti-patterns)

## Core Web Vitals 目标

| 度量衡 | 不错 | 工作需求 | 穷 |
|--------|------|------------|------|
| LCP 常规程序 (内容最丰富的绘画) |  • 2.5s |                                                              |  > 4.0s (单位:千兆赫) |
| INP 统计 (与下一张画的交互) |                                                                        |  ⁇  500ms(500ms) |  > 500毫秒( X) |
| CLS 密码 (累积布局移动) | ≤ 0.1 | ≤ 0.25 | > 0.25 |

## TTFB 诊断

 当 TTFB 缓慢时 ( > 800ms (千米) (千米))中,检查每个组件 DevTools 网络瀑布 :

- [ ] **DNS 分辨率** 缓慢 + 添加 `<link rel="dns-prefetch">` 或 `<link rel="preconnect">` 以已知来源命名
- [ ] **TCP/TLS 握手时** 慢 —— 启用 HTTP2,考虑边线部署,核实是否保持活动
- [ ] **服务器处理** 慢 – 配置后端, 检查慢查询, 添加缓存

##  前端检查列表

### 图像
- [ ] 图像使用现代格式 (网络P, AVIF)
- [ ] 图像大小反应灵敏 (`srcset` 财务报告和财务报告 `sizes`)
- [ ] 图像和 `<source>` 要素有明确的 `width` 财务报告和财务报告 `height` ( 在艺术方向上防止 CLS)
- [ ] 使用双面下图像 `loading="lazy"` 财务报告和财务报告 `decoding="async"`
- [ ] Hero/LCP 图像使用 `fetchpriority="high"` 而不懒惰的装载

### JavaScript
- [ ] 200KB gziped 下捆绑大小 (初始负载)
- [ ]  用动态分割代码 `import()` 路线和重型特征
- [ ] 已启用树摇动 (核查依赖船舶的无害环境管理和标记 `sideEffects: false`)
- [ ]  没有屏蔽 JavaScript 输入 `<head>` (使用情况 `defer` 或 `async`)
- [ ] 重计算卸载到 Web 工人 (适用时)
- [ ] `React.memo()` 重装同样道具的昂贵组件
- [ ] `useMemo()` / `useCallback()` 仅在特征分析显示有利的情况下
- [ ] 长期任务 ( > 50 分钟) 断开以保持主线线的可用性——INP的主要杠杆
- [ ] `yieldToMain` 长期循环内使用的模式,以便输入事件可以在块之间运行
- [ ] 可用的现代调度 API : `scheduler.yield()` (首选项), `scheduler.postTask()` 与优先事项, `isInputPending()` 只在有需要时才能产生
- [ ] `requestIdleCallback` 递延、非紧急工作 (分析冲浪、预画、热身)
- [ ] 事件处理人员推迟的非关键工作 ( 例如,分析、伐木)  因此,对交互的反应不会被延迟
- [ ] 装入的第三方脚本 `async` / `defer`被审计的大小, 当重时被外表挡住 (聊天部件, 嵌入式)

### CSS
- [ ] 关键 CSS 内嵌或预装
- [ ]  没有制动阻塞 CSS 用于非关键样式
- [ ]  没有 CSS- JS生产运行时间成本 (使用提取)

### 字体
- [ ] 限于2至3个字体家庭,每家2至3个重量 ( 每个额外的重量是另一个请求)
- [ ] 仅使用 WOFF2 格式 (最小, 通用支持 – 跳过 WOFF/TTF/EOT)
- [ ] 可能时自办 (第三方字体 CDNs 添加 DNS + TCP + TLS 回合)
- [ ] LCP 关键字体预装 : `<link rel="preload" as="font" type="font/woff2" crossorigin>`
- [ ] `font-display: swap` (或 `optional`  非临界值) 避免 FOIT 阻断渲染
- [ ] 已通过 `unicode-range` 来运送每页需要的格言
- [ ] 多个时考虑的可变字体 weights/styles 需要时 ( 一个文件取代许多文件)
- [ ] 倒置字体衡量标准 `size-adjust`, `ascent-override`, `descent-override`  在字体交换中减少 CLS
- [ ]  在自定义字体之前考虑的系统字体堆栈

### 网络
- [ ] 长期缓存的静态资产 `max-age` + 内容散列
- [ ] API 酌情缓存的答复 (`Cache-Control`)
- [ ] HTTP 第2条或 HTTP启用 3
- [ ] 资源已预先连接 (`<link rel="preconnect">`) 以已知来源命名
- [ ] `fetchpriority` 用于关键非图像资源 ( 例如,密钥 `<link rel="preload">`上层 `<script>`) - 不仅仅是在 `<img>`
- [ ]  无不必要的重定向

### 渲染
- [ ]  无布局抽打 (强制同步布局)
- [ ] 动画的使用 `transform` 财务报告和财务报告 `opacity` (GPU 加速)
- [ ] 长列表使用虚拟化 ( 例如, `react-window`)
- [ ]  没有不必要的整页重报
- [ ] 使用屏幕外区域 `content-visibility: auto` 与 `contain-intrinsic-size` 跳过 layout/paint  非可见区域
- [ ]  没有 `unload` 事件处理器和无 `Cache-Control: no-store` 打开 HTML 答复——保留 back/forward 缓存 (缓存) 资格

## 后端检查列表

### 数据库
- [ ]  无 N+1 查询模式 (使用急速加载/ 加入)
- [ ] 查询有适当的索引
- [ ] 列表结束点 ( 从不 `SELECT * FROM table`)
- [ ] 已配置连接集
- [ ] 启用了缓慢的查询记录

#### 查询计划
- [ ] `EXPLAIN ANALYZE` 被俘获 **在此之前** 修复,而不是仅仅在之后——这是基线
- [ ] `Seq Scan`  在大表格上理解:指数缺失,无法使用,或真正不值得
- [ ] 估计数与实际数 `rows=` 数量级之内 ( 如果没有,请在触摸索引前刷新统计数据)
- [ ]  没有 `Sort` 综合指数可吸收的节点
- [ ] 改变后重新检查计划——没有改变计划的指数被恢复

#### 指数战略
- [ ] 综合指数列顺序是:先平等,然后平等 range/sort
- [ ] 索引覆盖查询形状 (过滤 + 排序),不仅仅是一个柱子孤立
- [ ]  为热读路径考虑的覆盖索引 (仅限索引扫描可避免堆积获取)
- [ ]  不编制低选择性列索引 * 对主值*; 部分索引仍然服务于稀有值查询 (`WHERE status = 'failed'`)
- [ ] 用于查询应用函数的表达式索引 (`lower(email)`)
- [ ] 用于前置 Wildcard 搜索的完整文本或三格索引, 而非 B 树
- [ ]  在书写重的表格上测量的书写成本 (每一指数税 `INSERT`/`UPDATE`)
- [ ]  未使用和重复的索引已丢弃 (写作和买不买东西)

#### 连接集
- [ ]  每个进程一个人才库,而不是每个请求或每个模块
- [ ] `instances × pool max` 保留在数据库的下面 `max_connections`
- [ ] `connectionTimeoutMillis` 设定如此耗尽, 而不是永远排队
- [ ] 重新大小化前诊断的耗尽: 找到连接所在 (长交易, 缺失 `await`,泄露客户端)
- [ ]  无服务器 / 自动缩放由多功能代理前置 (pgbouncer, RDS 代理服务器) 而不是更大的游泳池

### API
- [ ] 反应时间 < 200毫秒 ( 第95页)
- [ ]  在请求处理器中没有同步重计算
- [ ] 批量操作而不是单个呼叫的循环
- [ ] 响应压缩 (gzip/brotli)
- [ ] 适当的缓存 (记忆、 Redis、 CDN)

### 基础设施
- [ ] 静态资产的CDN
- [ ] 服务器位于用户附近 (或边缘部署)
- [ ] 水平缩放已配置 (需要的话)
- [ ] 负载平衡器的健康检查终点

## 缓存策略

 决定材料 (哪一层, 哪些是无效策略, 哪些是永远不缓存) 生活状况 `performance-optimization`  本节涵盖 read/write 模式和核对表。

### 读写图案

|  图案 | 如何运作 | 使用时间 | 小心点 |
|---|---|---|---|
| **缓存侧面** (懒惰) | 应用检查缓存, 在错误读取源头和插件上 | 默认选择; 读重, 耐寒第一击 |  每个失手者都会击中原产地 所以需要盖章保护 |
| **读取** | 缓存层本身负载到错失 |  你想要一个地方的载荷路径,而不是每个呼叫站点的 | 隐藏来源延迟; 慢源看起来像缓存 |
| **写入** | 写到缓存和源头,同步 | 读取绝对不能看到写完之后的陈旧值 |  在每次写时添加缓存间隔 |
| **书写后方** (写回) | 写入命中缓存, 源头同步更新 | 书写重,而源头就是瓶颈 |  如果缓存在冲洗前死亡, 数据丢失窗口。 您需要耐久性 |

### 负缓冲

缓存 *缺席*  在每次检查中都漏掉的密钥 ( 在循环中探测的不存在的用户ID, 404 个资产) 向源头发送每个请求, 源头是一个缓存, 它只保护幸福路径 。

- 将“未找到”的明信片与 a 存储 **短点** TTL 比正数条目
- 保持负 TTL 的短度, 以便新创建的记录迅速出现
- 永远不要让一个来源 *错误* 变成一个负缓存项, 或者一个失败的分钟变成很多

### 请求连接 (纹章保护)

 一个重算, N 服务员。 防止热键过期将全部同时负载发送到源头 :

```typescript
const inFlight = new Map<string, Promise<unknown>>();

function loadOnce<T>(key: string, fetcher: () => Promise<T>): Promise<T> {
  const existing = inFlight.get(key) as Promise<T> | undefined;
  if (existing) return existing;
  const p = fetcher().finally(() => inFlight.delete(key));
  inFlight.set(key, p);
  return p;
}
```

 对于共享缓存,同一想法需要一个分布式锁,或者 `stale-while-revalidate` 服务员们只服务于陈旧的价值观,而不服务于屏蔽。

### 缓存检查表
- [ ] 缓存的呼叫首先被测量为昂贵 (快速呼叫中, 增加跳动, 买不到任何东西)
- [ ] Read/write 比例说明缓存的理由 (重读次数远多于写数)
- [ ] 缓存密钥包括每个输入, 答案不同 : 租户、 查看者、 本地、 权限、 特性旗
- [ ]  没有在未识别用户的密钥下缓存的每个用户数据
- [ ] 选择一个无效策略 (TTL, (意大利语) event/tag,或版本键),不是偶然的组合
- [ ]  可接受 Staleity 窗口已写下, 而非任何 TTL 键入的表示
- [ ] 热键上的印章保护 (串联、锁定或 `stale-while-revalidate`)
- [ ] 负结果缓存, TTL 较短; 源错误从未缓存
- [ ] 驱逐政策和内存上限设定 ( 未绑定的缓存是内存泄漏)
- [ ] 监测命中率——一个缓存无人计量是假设,一个低命中率是纯粹的间接费用
- [ ]  没有缓存的, 其扭曲度是正确性错误 (余额、权限、结账时的库存)

## 测量命令

### INP 实地数据和 DevTools 工作流程

1. **外地数据第一** - 检查 [CRUX 维斯](https://developer.chrome.com/docs/crux/vis) 或用于在优化前使用 INP 的 RUM 工具
2. **识别缓慢的相互作用** - 打开 DevTools + 业绩小组 + 在互动时记录; 寻找由 clicks/keystrokes
3. **中程 Android 测试** —— INP 发行时往往只在较慢的硬件上浮出水面;使用真正的设备或 DevTools CPU 调速 (4####6x减速)

```bash
# Lighthouse CLI
npx lighthouse https://localhost:3000 --output json --output-path ./report.json

# Bundle analysis
npx webpack-bundle-analyzer stats.json
# or for Vite:
npx vite-bundle-visualizer

# Check bundle size
npx bundlesize

# Web Vitals in code
import { onLCP, onINP, onCLS } from 'web-vitals';
onLCP(console.log);
onINP(console.log);
onCLS(console.log);

# INP with interaction-level detail (attribution build)
import { onINP } from 'web-vitals/attribution';
onINP(({ value, attribution }) => {
  const { interactionTarget, inputDelay, processingDuration, presentationDelay } = attribution;
  console.log({ value, interactionTarget, inputDelay, processingDuration, presentationDelay });
});
```

## 常见反恐怖分子

| 反治疗者 | 影响 | 修补 |
|---|---|---|
| N+1 查询 | 线性 DB 负载增长 | 使用连接、 包含或批量加载 |
|  无约束的查询 | 内存耗竭、超时 | 总是粘贴, 添加 LIMIT |
| 缺少索引 | 数据增长后读取速度缓慢 | 添加索引 filtered/sorted 栏 |
|  在不阅读计划的情况下编制索引 | 已支付写作费用, 读取未证实的收益 | `EXPLAIN ANALYZE` 前后; 如果计划不变, 返回 |
| 冗余/未使用的索引 | 每一篇都付了钱 | 审计用量统计, 放下没有读到的东西 |
|  每个请求的连接池 | 耗尽 `max_connections` 负载 |  每个进程一个集合; 无服务器代理 |
| 缺少查看器缓存密钥 |  一个用户的数据提供给另一个用户 | 租户、 查看者、 本地人、 权限的密钥 |
|  无限制缓存 | 穿着最优化服装的记忆漏水 | 设定拆迁政策和内存上限 |
| 缓存粘贴在热键上 | 原产地在期满时同时满载 | 杂货店漏了,或者 `stale-while-revalidate` |
| 排版打压 | 扬克,丢掉的画框 | 批次 DOM 读取,然后批次写出 |
|  未优化图像 | 慢 LCP, 浪费带宽 | 使用 WebP 、 响应大小、 懒惰负载 |
| 大型捆绑 | 慢时间到互动 | 代码分割, 树摇, 审计道具 |
| 屏蔽主线程 | 贫穷的INP,没有反应 UI | 弹簧长任务 `scheduler.yield()` / `yieldToMain`,卸载到 Web 工人 |
| 内存泄漏 | 不断增长的记忆, 最终崩溃 | 清理收听器,间隔,调试器 |
