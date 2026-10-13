# 优化模式

 第3步中命名的反标 `performance-optimization` 技能。 技能说明要查找的规则和签名; 此文件显示一个具体执行。 在步骤2 识别瓶颈后打开您需要的段落 。 它不是要从上到下读 。

核对表、测量命令、 TTFB 诊断和缓存 read/write 战略在 `../../../references/performance-checklist.md`.

## N+1 查询 (后端)

```typescript
// BAD: N+1 — one query per task for the owner
const tasks = await db.tasks.findMany();
for (const task of tasks) {
  task.owner = await db.users.findUnique({ where: { id: task.ownerId } });
}

// GOOD: Single query with join/include
const tasks = await db.tasks.findMany({
  include: { owner: true },
});
```

##  未绑定的数据获取

```typescript
// BAD: Fetching all records
const allTasks = await db.tasks.findMany();

// GOOD: Paginated with limits
const tasks = await db.tasks.findMany({
  take: 20,
  skip: (page - 1) * 20,
  orderBy: { createdAt: 'desc' },
});
```

## 查询忽略其索引

“添加索引”是猜测。查询计划是测量:

```sql
EXPLAIN ANALYZE
SELECT id, title FROM tasks
WHERE owner_id = 42 ORDER BY created_at DESC LIMIT 20;
```

产出中的三件事决定了固定:

|  你看见的 |  这是什么意思 |
|---|---|
| `Seq Scan` 在一个你期望索引的大桌子上 |  此上游没有可用的索引 |
| 估计数 `rows=` 以数量顺序从实际数量中扣除 | Stale统计; 计划员选择了不良信息 |
| A `Sort` 扫描上方的节点 | 索引涵盖过滤器,但不涵盖 `ORDER BY` |

2003年12月31日终了的两年期 **查询的形状**,而不是孤立的列。在综合索引中,平等列首先出现,然后是范围或排序列:

```sql
CREATE INDEX idx_tasks_owner_created ON tasks (owner_id, created_at DESC);
```

** 当一个指数无益时:**

| 情况 |  为什么 |
|---|---|
| 选择性低, 查询主值 (a `status` 占95%的栏数 `active`,过滤在 `active`) | 顺序扫描真正便宜; 计划者会忽略索引。 过滤稀有值是相反的, 部分索引为它提供了很好的服务 。 |
|  主通配符 (`LIKE '%term'`) | B- 树没有前缀就无法寻求; 需要三进制或全文 |
| 列上的函数 (`WHERE lower(email) = ?`) | 普通列索引无法使用; 将表达式索引改为 |
| 写重的桌子 |  每个指数都是对每个指数的征税 `INSERT`/`UPDATE`; 测量写入成本, 而不仅仅是读取收益 |

重新运行 `EXPLAIN ANALYZE` 之后。没有改变计划的指数是回转 (步骤4),而且它不是免费的:它仍然花费在每篇写作上。

## 连接池耗尽

签字具有特殊性: ** 每张** 结束点一时减慢,缓慢的时间被花在等待连接而不是执行上,数据库报告大部分是闲散的会话.

```typescript
// BAD: a pool per request or per module — under serverless this multiplies
// by instance count and exhausts the database's connection limit
// GOOD: one pool per process, sized against the database's ceiling
const pool = new Pool({
  max: 10,                        // instances × max must stay under max_connections
  idleTimeoutMillis: 30_000,
  connectionTimeoutMillis: 5_000, // fail fast instead of queueing forever
});
```

** 大一点不快。**  一个大于数据库同时执行内容的集合只是将队列从您的应用程序移到数据库中, 在那里很难看到。 当实例计数被取消时 ( 无服务器, 自动缩放),用于多轴连接的代理 (pgbouncer, RDS 代理服务器)  是修补,不是更高 `max`.

## 缺少图像优化 ( 前端 :)

```html
<!-- BAD: No dimensions, no format optimization -->
<img src="/hero.jpg" />

<!-- GOOD: Hero / LCP image — art direction + resolution switching, high priority -->
<!--
  Two techniques combined:
  - Art direction (media): different crop/composition per breakpoint
  - Resolution switching (srcset + sizes): right file size per screen density
-->
<picture>
  <!-- Mobile: portrait crop (8:10) -->
  <source
    media="(max-width: 767px)"
    srcset="/hero-mobile-400.avif 400w, /hero-mobile-800.avif 800w"
    sizes="100vw"
    width="800"
    height="1000"
    type="image/avif"
  />
  <source
    media="(max-width: 767px)"
    srcset="/hero-mobile-400.webp 400w, /hero-mobile-800.webp 800w"
    sizes="100vw"
    width="800"
    height="1000"
    type="image/webp"
  />
  <!-- Desktop: landscape crop (2:1) -->
  <source
    srcset="/hero-800.avif 800w, /hero-1200.avif 1200w, /hero-1600.avif 1600w"
    sizes="(max-width: 1200px) 100vw, 1200px"
    width="1200"
    height="600"
    type="image/avif"
  />
  <source
    srcset="/hero-800.webp 800w, /hero-1200.webp 1200w, /hero-1600.webp 1600w"
    sizes="(max-width: 1200px) 100vw, 1200px"
    width="1200"
    height="600"
    type="image/webp"
  />
  <img
    src="/hero-desktop.jpg"
    width="1200"
    height="600"
    fetchpriority="high"
    alt="Hero image description"
  />
</picture>

<!-- GOOD: Below-the-fold image — lazy loaded + async decoding -->
<img
  src="/content.webp"
  width="800"
  height="400"
  loading="lazy"
  decoding="async"
  alt="Content image description"
/>
```

## 不必要的重新提交 (React)

```tsx
// BAD: Creates new object on every render, causing children to re-render
function TaskList() {
  return <TaskFilters options={{ sortBy: 'date', order: 'desc' }} />;
}

// GOOD: Stable reference
const DEFAULT_OPTIONS = { sortBy: 'date', order: 'desc' } as const;
function TaskList() {
  return <TaskFilters options={DEFAULT_OPTIONS} />;
}

// Use React.memo for expensive components
const TaskItem = React.memo(function TaskItem({ task }: Props) {
  return <div>{/* expensive render */}</div>;
});

// Use useMemo for expensive computations
function TaskStats({ tasks }: Props) {
  const stats = useMemo(() => calculateStats(tasks), [tasks]);
  return <div>{stats.completed} / {stats.total}</div>;
}
```

##  大套装大小

```typescript
// Modern bundlers (Vite, webpack 5+) handle named imports with tree-shaking automatically,
// provided the dependency ships ESM and is marked `sideEffects: false` in package.json.
// Profile before changing import styles — the real gains come from splitting and lazy loading.

// GOOD: Dynamic import for heavy, rarely-used features
const ChartLibrary = lazy(() => import('./ChartLibrary'));

// GOOD: Route-level code splitting wrapped in Suspense
const SettingsPage = lazy(() => import('./pages/Settings'));

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <SettingsPage />
    </Suspense>
  );
}
```

## 缺少缓存 (后端)

缓存所花费的制作和阅读比它变化的多得多。 缓存一个已经很快的查询会增加一个网络跳跃、一个 staleness bug 以及一个可以维护的驱逐政策, 以换取一无所有。

**故意选择图层 :**

| 层 | 可见 | 使用时间 | 费用 |
|---|---|---|---|
| 进行中 (`Map`、路运站) |  一个实例 |  小的,热的, 过敏的固态是可以接受的 |  每个实例独立漂移;无效只达到一个 |
| 共享 (瑞迪丝,默卡赫德) |  所有实例 | 情况必须一致,否则重算值会很贵 |  一个网络 hop, 和另一个运行和监视的服务 |
| CDN/边缘 | 每个人,每名 URL | 答复是公开的,对给定的密钥相同。 | 无效化是困难的部分; 假设您无法快速记得不良反应 |

```typescript
// Cache frequently-read, rarely-changed data
const CACHE_TTL = 5 * 60 * 1000; // 5 minutes
let cachedConfig: AppConfig | null = null;
let cacheExpiry = 0;

async function getAppConfig(): Promise<AppConfig> {
  if (cachedConfig && Date.now() < cacheExpiry) {
    return cachedConfig;
  }
  cachedConfig = await db.config.findFirst();
  cacheExpiry = Date.now() + CACHE_TTL;
  return cachedConfig;
}

// HTTP caching headers for static assets
app.use('/static', express.static('public', {
  maxAge: '1y',           // Cache for 1 year
  immutable: true,        // Never revalidate (use content hashing in filenames)
}));

// Cache-Control for API responses
res.set('Cache-Control', 'public, max-age=300'); // 5 minutes
```

**关键设计决定正确性.** 每一个修改响应的输入都属于密钥中: 租户, 地方, 权限, 特征标记。 省略查看器的密钥是用户的数据如何被送达给另一个用户, 以及作为性能胜利的船舶 。

**选择一个无效策略, 而不是 3 :**

| 战略 | 权衡 |
|---|---|
| TTL 电话 |  最简单。 您接受到 TTL 的 Staleity, 所以请明确显示可接受的窗口 。 |
|  基于事件或标签 | 刚开始写作,但写作者现在必须知道缓存的地形 |
| 版本密钥 (`user:42:profile:v7`) | 永远不要失效,只要停止读旧密钥。 |

**护持印 ⁇ .** 热键过期, 每一个同时提出的请求都一起错过, 而源头会同时承担全部负载, 也就是缓存会变成一个停用而不是防止。 在单个请求重算时按住 stale 。 (`stale-while-revalidate`),或者连在一起 错过一个飞行中的承诺,所以N服务员 导致一个重算。

** 不缓存 :** 任何其固态是正确错误的事物 (余额、权限、结账时的库存),或者在不识别用户的密钥下按每个用户的数据。 `../../../references/performance-checklist.md#caching-strategies` 用于请求串联、写策略、负缓存和缓存核对表。
