# 硬化图案

守则模式和决策辅助工具 `security-and-hardening` 技能 。 技能说明每个规则; 此文件显示它的具体执行。 当您到达代码时打开您需要的部分 。 它不是要从上到下读的 。

共享核对表 (威胁建模、承诺前检查、安全信头、CORS、软件包管理矩阵、安装标记门和 OWASP 快速参考表格) 住在这里 `../../../references/security-checklist.md`.

## OWASP  前10名 预防模式

 这些是预防模式,不是排名。关于2021年的订单,请参见快速参考表。 `../../../references/security-checklist.md#owasp-top-10-quick-reference`.

### 注射量

```typescript
// BAD: SQL injection via string concatenation
const query = `SELECT * FROM users WHERE id = '${userId}'`;

// GOOD: Parameterized query
const user = await db.query('SELECT * FROM users WHERE id = $1', [userId]);

// GOOD: ORM with parameterized input
const user = await prisma.user.findUnique({ where: { id: userId } });
```

### 损坏的认证

```typescript
// Password hashing
import { hash, compare } from 'bcrypt';

const SALT_ROUNDS = 12;
const hashedPassword = await hash(plaintext, SALT_ROUNDS);
const isValid = await compare(plaintext, hashedPassword);

// Session management
app.use(session({
  secret: process.env.SESSION_SECRET,  // From environment, not code
  resave: false,
  saveUninitialized: false,
  cookie: {
    httpOnly: true,     // Not accessible via JavaScript
    secure: true,       // HTTPS only
    sameSite: 'lax',    // CSRF protection
    maxAge: 24 * 60 * 60 * 1000,  // 24 hours
  },
}));
```

### 跨场脚本 (一般事务)

```typescript
// BAD: Rendering user input as HTML
element.innerHTML = userInput;

// GOOD: Use framework auto-escaping (React does this by default)
return <div>{userInput}</div>;

// If you MUST render HTML, sanitize first
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(userInput);
```

### 中断访问控制

```typescript
// Always check authorization, not just authentication
app.patch('/api/tasks/:id', authenticate, async (req, res) => {
  const task = await taskService.findById(req.params.id);

  // Check that the authenticated user owns this resource
  if (task.ownerId !== req.user.id) {
    return res.status(403).json({
      error: { code: 'FORBIDDEN', message: 'Not authorized to modify this task' }
    });
  }

  // Proceed with update
  const updated = await taskService.update(req.params.id, req.body);
  return res.json(updated);
});
```

### 安全配置错误

```typescript
// Security headers (use helmet for Express)
import helmet from 'helmet';
app.use(helmet());

// Content Security Policy
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'"],
    styleSrc: ["'self'", "'unsafe-inline'"],  // Tighten if possible
    imgSrc: ["'self'", 'data:', 'https:'],
    connectSrc: ["'self'"],
  },
}));

// CORS — restrict to known origins
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || 'http://localhost:3000',
  credentials: true,
}));
```

### 敏感数据曝光

```typescript
// Never return sensitive fields in API responses
function sanitizeUser(user: UserRecord): PublicUser {
  const { passwordHash, resetToken, ...publicFields } = user;
  return publicFields;
}

// Use environment variables for secrets
const API_KEY = process.env.STRIPE_API_KEY;
if (!API_KEY) throw new Error('STRIPE_API_KEY not configured');
```

### 服务器- 服务器请求伪造 (SSRF( 战略支助部队))

服务器每次获取一个 URL 用户受到影响——webhooks,"import from URL",图像代理,链接预览——攻击者可以瞄准内部服务. (云元数据, `localhost`,私人实施伙伴).

```typescript
// BAD: fetch whatever the user gives you
await fetch(req.body.webhookUrl);

// GOOD: allowlist scheme + host, reject if ANY resolved IP is private, forbid redirects
import { lookup } from 'node:dns/promises';
import ipaddr from 'ipaddr.js';

const ALLOWED_HOSTS = new Set(['hooks.example.com']);

async function assertSafeUrl(raw: string): Promise<URL> {
  const url = new URL(raw);
  if (url.protocol !== 'https:') throw new Error('https only');
  if (!ALLOWED_HOSTS.has(url.hostname)) throw new Error('host not allowed');
  // Resolve ALL records; a single private/reserved address fails the check.
  const addrs = await lookup(url.hostname, { all: true });
  if (addrs.some((a) => ipaddr.parse(a.address).range() !== 'unicast')) {
    throw new Error('private/reserved IP');
  }
  return url;
}

await fetch(await assertSafeUrl(req.body.webhookUrl), { redirect: 'error' });
```

 那个... `range() !== 'unicast'` 检查覆盖回路, 链接- 本地 `169.254.169.254` (云元数据, # 1 SSRF 目标),在IPv4和IPv6之间有私有和独特的本地范围.

**Caveat——这仍然有一个TOCTOU缺口.** `fetch`  在检查后再次解析 DNS, 因此使用短 TTL 记录的攻击者可以在验证和连接之间重新连接到内部 IP。 对于高风险的表面, 一次解析并连接到被绑定的 IP, 或者在前面放置过滤代理 (`request-filtering-agent` / `ssrf-req-filter`).

## 输入验证模式

### Schema 边界验证

```typescript
import { z } from 'zod';

const CreateTaskSchema = z.object({
  title: z.string().max(200).trim().min(1),
  description: z.string().max(2000).optional(),
  priority: z.enum(['low', 'medium', 'high']).default('medium'),
  dueDate: z.string().datetime().optional(),
});

// Validate at the route handler
app.post('/api/tasks', async (req, res) => {
  const result = CreateTaskSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(422).json({
      error: {
        code: 'VALIDATION_ERROR',
        message: 'Invalid input',
        details: result.error.flatten(),
      },
    });
  }
  // result.data is now typed and validated
  const task = await taskService.create(result.data);
  return res.status(201).json(task);
});
```

###  文件上传安全

```typescript
// Restrict file types and sizes
const ALLOWED_TYPES = ['image/jpeg', 'image/png', 'image/webp'];
const MAX_SIZE = 5 * 1024 * 1024; // 5MB

function validateUpload(file: UploadedFile) {
  if (!ALLOWED_TYPES.includes(file.mimetype)) {
    throw new ValidationError('File type not allowed');
  }
  if (file.size > MAX_SIZE) {
    throw new ValidationError('File too large (max 5MB)');
  }
  // Don't trust the file extension — check magic bytes if critical
}
```

### 衍生路径上的破坏性操作

 删除、移动或覆盖仅与给目标命名的值一样安全。从内核、工作有效载荷或兄弟姐妹服务中读取该值可以证明它在哪里 *抵达*,而不是谁 *写*  它 — 另一个进程的命令行是像窗体字段一样由攻击者控制的。 ("绝对的路径,至少一个目录深") 证明了良好的造型,并被误认为授权;这就是清理常规如何删除根而不是叶子.

 在发出破坏性呼吁之前,要求所有三个目标: **允许列表根** (解析符号链接后进行比较, 永远不要在原始字符串上); 至少是一个级别 **下级** 根,所以根 从来不是目标本身; 它携带 **证明这是你的**,读 *在此之前* 拒绝时,登录被拒绝的目标,并停止: 清理后, 回到更广泛的默认路径就是这些卫士所反对的失败。 `../../../references/security-checklist.md#destructive-path-operations`.

 两个限制,因为检查读得比它更强. 树内的一个标记是自我验证——任何可以在那里写的东西都可以写标记——所以预期的拥有者必须来自认证状态,标记需要完整性保护. (限制性所有权,或MAC)  在它算作授权之前, 并解决路径,然后运行在 *名称* 是一个 check/use  在无信任进程可以互换祖先的地方有种族:在共享的音量上,用描述符控制目标,并使用不跟随的,根底的操作,或者确保等级结构在持续期间不能改变.

## 依赖性审计

软件包管理审计报告已知的咨询;它们不能证明软件包是可信的,也不能证明易懂的代码。使用此决定树:

```
The native package-manager audit reports a vulnerability
├── Severity: critical or high
│   ├── Is the vulnerable code reachable in runtime, build, test, or deployment paths?
│   │   ├── YES --> Fix immediately (update, patch, or replace the dependency)
│   │   └── NO (confirmed unused across those paths) --> Fix soon, but not a blocker
│   └── Is a fix available?
│       ├── YES --> Update to the patched version
│       └── NO --> Check for workarounds, consider replacing the dependency, or add to allowlist with a review date
├── Severity: moderate
│   ├── Reachable in production? --> Fix in the next release cycle
│   └── Dev-only? --> Fix when convenient, track in backlog
└── Severity: low
    └── Track and fix during regular dependency updates
```

**关键问题:**
-  您的代码路径中是否实际调用了脆弱函数 ?
- 依赖是运行时的依赖还是只依赖虔诚?
-  鉴于您的部署背景,脆弱性是否可加以利用? ( 例如,仅在客户端的应用程序中的服务器侧弱点)?

推迟修补时,记录原因并设定复议日期.

## 费率限制

```typescript
import rateLimit from 'express-rate-limit';

// General API rate limit
app.use('/api/', rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,                   // 100 requests per window
  standardHeaders: true,
  legacyHeaders: false,
}));

// Stricter limit for auth endpoints
app.use('/api/auth/', rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 10,  // 10 attempts per 15 minutes
}));
```

** 一旦有一个以上的进程,就计算在共享商店中。** `express-rate-limit` 默认情况下将计数器保存在进程内存中。在负载平衡器后面,每个实例都有自己的计数,所以有效的限制是 `max × instances`; 在无服务器或边缘运行时, 新的引用从零开始, 因此上面的认证限制可能永远不会开火 。 通过共享 `store` (重复通过 `rate-limit-redis`),或使用 HTTP- 长寿命TCP连接不起作用的基于限制器 (举例来说, `@upstash/ratelimit`):

```typescript
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const authLimiter = new Ratelimit({
  redis: Redis.fromEnv(),                       // UPSTASH_REDIS_REST_URL + _TOKEN
  limiter: Ratelimit.slidingWindow(10, '15 m'), // 10 attempts per 15 minutes, across all instances
});
const { success } = await authLimiter.limit(`login:${req.ip}`);
if (!success) return res.status(429).end();
```

## 保密管理

```
.env files:
  ├── .env.example  → Committed (template with placeholder values)
  ├── .env          → NOT committed (contains real secrets)
  └── .env.local    → NOT committed (local overrides)

.gitignore must include:
  .env
  .env.local
  .env.*.local
  *.pem
  *.key
```

**执行前总是检查 :**
```bash
# Check for accidentally staged secrets
git diff --cached | grep -i "password\|secret\|api_key\|token"
```

** 如果一个秘密是永远的,旋转它。**  删除线条或重写历史是不够的——假设它到达遥控器的瞬间就受损了. Revoke and reissue the key first, 然后从历史中清除它.

## 数据分类

 在添加字段时分类个人数据,因此导出和删除请求可以稍后找到:

| 类 | 实例 | 处理 |
|---|---|---|
| ** 非个人** | 合计数、匿名数 | 正常处理 |
| **个人资料 (临 二)** | 姓名,电子邮件,IP, device/user 身份证 | 最小化、出入控制,包括在 export/delete |
| **敏感** | 健康、金融、地点、生物鉴别、政府身份证 有关未成年人的任何信息 | 收集的额外依据,更严格的访问,往往加密+审计记录 |

## LLM 输出处理

```typescript
// BAD: trusting model output as a command or as markup
const sql = await llm.generate(`Write SQL for: ${userQuestion}`);
await db.query(sql);                                   // arbitrary query execution
container.innerHTML = await llm.reply(userMessage);   // stored XSS, via the model

// GOOD: model output is data — parse defensively, then validate, then encode
let intent;
try {
  intent = CommandSchema.parse(JSON.parse(await llm.replyJson(userMessage)));
} catch {
  throw new ValidationError('unexpected model output'); // JSON.parse or schema failed
}
await runAllowlistedAction(intent.action, intent.params);
container.textContent = await llm.reply(userMessage);
```
