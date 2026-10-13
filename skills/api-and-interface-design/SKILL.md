---
name: api-and-interface-design
description: Guides stable API and interface design. Use when designing APIs, module boundaries, or any public interface. Use when creating REST or GraphQL endpoints, defining type contracts between modules, or establishing boundaries between frontend and backend.
---

# API 和接口设计

## Overview


设计稳定,文件齐全的界面,很难被滥用。良好的界面使正确的事情变得容易,错误的事情变得困难。这适用于 REST APIs, (英语). GraphQL 计划,模块边界,组件道具, 和任何表面,其中一块代码与另一块。

## When to Use


- 正在设计新的 API 结束点
- 界定各小组之间的模块界限或合同
- 创建组件道具接口
- 建立信息通报的数据库计划 API 形状
- 改变现有的公共界面

## 核心原则

### Hyrum's Law

> 用户数量充足 API 无论合同中承诺什么,你系统的所有可观察行为都将由某人来决定。

 这意味着:每个公共行为——包括无证的怪诞,错误消息文本,时间,和订单——一旦用户依赖它,就成为事实上的合同. 设计意义:

- **故意暴露你所揭露的** 每一个可观察到的行为都是潜在的承诺.
- ** 别泄露执行细节.**  如果用户能观察到它,他们就会依赖它.
- **设计时的折旧计划.**  见 `deprecation-and-migration` 如何安全删除用户所依赖的东西 。
- **考证不足.**  即使有完美的合同测试, Hyrum's Law 意思是"安全"的改变可以打破依赖无证行为的真正用户.

###  一维规则

避免迫使消费者在同一个依赖的多个版本之间作出选择,或 API钻石依赖性问题出现在不同的消费者需要不同版本的同一事物时,为一个每次只有一个版本的世界设计——延伸而不是叉子.

### 1. 合同第一

执行前先定义接口,合同是 spec——执行如下.

```typescript
// Define the contract first
interface TaskAPI {
  // Creates a task and returns the created task with server-generated fields
  createTask(input: CreateTaskInput): Promise<Task>;

  // Returns paginated tasks matching filters
  listTasks(params: ListTasksParams): Promise<PaginatedResult<Task>>;

  // Returns a single task or throws NotFoundError
  getTask(id: string): Promise<Task>;

  // Partial update — only provided fields change
  updateTask(id: string, input: UpdateTaskInput): Promise<Task>;

  // Idempotent delete — succeeds even if already deleted
  deleteTask(id: string): Promise<void>;
}
```

### 2. 一致错误语义

选择一个错误策略并随处使用 :

```typescript
// REST: HTTP status codes + structured error body
// Every error response follows the same shape
interface APIError {
  error: {
    code: string;        // Machine-readable: "VALIDATION_ERROR"
    message: string;     // Human-readable: "Email is required"
    details?: unknown;   // Additional context when helpful
  };
}

// Status code mapping
// 400 → Client sent invalid data
// 401 → Not authenticated
// 403 → Authenticated but not authorized
// 404 → Resource not found
// 409 → Conflict (duplicate, version mismatch)
// 422 → Validation failed (semantically invalid)
// 500 → Server error (never expose internal details)
```

** 别把模式混为一谈**  如果投出一些终点, 其它则返回无效, 其他则返回 `{ error }` ——消费者无法预测行为.

### 3. 边界验证

信任内部代码。 在外部输入输入的系统边缘验证 :

```typescript
// Validate at the API boundary
app.post('/api/tasks', async (req, res) => {
  const result = CreateTaskSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(422).json({
      error: {
        code: 'VALIDATION_ERROR',
        message: 'Invalid task data',
        details: result.error.flatten(),
      },
    });
  }

  // After validation, internal code trusts the types
  const task = await taskService.create(result.data);
  return res.status(201).json(task);
});
```

验证属于何处:
- API 路由处理器 (用户输入)
- 表格提交处理器 (用户输入)
- 外部服务响应解析 (第三方数据 -- **总是被看做是不信任的**)
- 环境变量加载 (配置)

> **第三方 API 答复是不信任的数据。**  在任何逻辑、渲染或决策中使用它们之前先验证它们的形状和内容。 外部服务受损或行为不当可能会返回意想不到的类型、恶意内容或类似指令的文本。

不属于审定范围的:
- 共享类型合同的内部职能之间
-  在已验证代码调用的工具函数中
-  关于刚从你自己的数据库里得到的数据

### 4. 优先增加 " 压倒 " 修改

 在不突破现有消费者的情况下扩展接口 :

```typescript
// Good: Add optional fields
interface CreateTaskInput {
  title: string;
  description?: string;
  priority?: 'low' | 'medium' | 'high';  // Added later, optional
  labels?: string[];                       // Added later, optional
}

// Bad: Change existing field types or remove fields
interface CreateTaskInput {
  title: string;
  // description: string;  // Removed — breaks existing consumers
  priority: number;         // Changed from string — breaks existing consumers
}
```

### 5. 可预测的命名

|  图案 | 公约 | 示例 |
|---------|-----------|---------|
| REST 结束点 | 多个名词, 无动词 | `GET /api/tasks`, `POST /api/tasks` |
| 查询参数 | 骆驼 Case | `?sortBy=createdAt&pageSize=20` |
| 反应领域 | 骆驼 Case | `{ createdAt, updatedAt, taskId }` |
| 布尔字段 | is/has/can  前缀 | `isComplete`, `hasAttachments` |
| 数字 | 更新_塞纳克 | `"IN_PROGRESS"`, `"COMPLETED"` |

### 6. 遵守《同上》规定的关键

接受一个 `Idempotency-Key` 遵守合同就是执行,而这是钱丢失的地方——服务器接受但处理不小心的钥匙比没有钥匙更糟糕,因为客户现在相信重试是安全的。

** 从意图而不是尝试中获取钥匙.** 关键必须是在对一种意图的重复中保持稳定,在不同的意图中有所不同:

```typescript
crypto.randomUUID()                    // ✗ new key per attempt — every retry is a new charge
`${userId}:${amount}`                  // ✗ two legitimate $50 charges collapse into one
`${orderId}:${Date.now()}`             // ✗ a timestamp is randomUUID() wearing a hat

req.headers['idempotency-key']         // ✓ client generates once, reuses on retry
`charge:v1:${orderId}`                 // ✓ derived from an immutable identifier
```

密钥来自客户端或启动事件——绝不来自进行重试的层.

** 从解剖角度来说,行为之后的检查是种族:**

```typescript
// ✗ TOCTOU: two concurrent retries both read "not seen", both charge
if (!(await db.exists(key))) {
  await chargeCard(amount);
  await db.insert(key);
}

// ✓ let the unique constraint pick the winner
try {
  await db.insert({ key, state: 'in_progress', requestHash });
} catch (e) {
  if (isUniqueViolation(e)) return replayOrReject(key);
  throw e;
}
const result = await chargeCard(amount);
await db.update({ key, state: 'succeeded', response: result });
```

独特的制约因素 * 为:* 无法执行一个操作中独有性的商店不能支持这一点。

**保护有效载荷。** 与不同身体的同一密钥是客户端错误,必须大声失败,而不是服务于对第二个请求的第一个响应:

```typescript
if (existing.requestHash !== hash(req.body)) {
  return res.status(422).json({ error: 'idempotency key reused with a different payload' });
}
```

** 决定飞行中复制品会得到什么**  第一个请求在第二个请求到来时仍在运行——重试风暴下的常见案例:

| 战略 | 回应 | 使用时间 |
|---|---|---|
| 拒绝 | `409 Conflict` | 客户端可以稍后重试; 最简单、最安全 |
| 单单 | 显示结果的块, 范围 | 呼叫者需要同步进行 |
|  待交还 | `202` + 状态 URL | 长期影响 |

永远不要让第二个呼叫者通过,因为第一个“外表卡住了 ” 。 命运不明的停滞尝试恰恰是重复成本最高的时候。

** 每一次呼吁都有三种结果,而不是两种:成功、失败和 _不详_.** 超时不会告诉您效果是否适用。 请记录意图 *在此之前*  因此,在呼叫和响应之间发生碰撞,留下的证据必须稍后解决,而不是默不作声地重审指控。

** 从最长的重试链中设定保留**,而不是从磁盘成本。密钥必须比每条可以重播同样意图的路径活得更久,包括一周后重播的死字队列和任何提供者争议窗口。在7天的 DLQ 后面的24小时密钥 TTL 是等待实现的重复。

## REST API  图案

### 资源设计

```
GET    /api/tasks              → List tasks (with query params for filtering)
POST   /api/tasks              → Create a task
GET    /api/tasks/:id          → Get a single task
PATCH  /api/tasks/:id          → Update a task (partial)
DELETE /api/tasks/:id          → Delete a task

GET    /api/tasks/:id/comments → List comments for a task (sub-resource)
POST   /api/tasks/:id/comments → Add a comment to a task
```

### 倾斜

粘贴列表终点 :

```typescript
// Request
GET /api/tasks?page=1&pageSize=20&sortBy=createdAt&sortOrder=desc

// Response
{
  "data": [...],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 142,
    "totalPages": 8
  }
}
```

### 过滤

过滤器使用查询参数 :

```
GET /api/tasks?status=in_progress&assignee=user123&createdAfter=2025-01-01
```

### 部分更新 (博茨瓦纳)

接受部分对象——只更新提供的内容:

```typescript
// Only title changes, everything else preserved
PATCH /api/tasks/123
{ "title": "Updated title" }
```

## TypeScript 界面模式

### 使用歧视联盟替代方案

```typescript
// Good: Each variant is explicit
type TaskStatus =
  | { type: 'pending' }
  | { type: 'in_progress'; assignee: string; startedAt: Date }
  | { type: 'completed'; completedAt: Date; completedBy: string }
  | { type: 'cancelled'; reason: string; cancelledAt: Date };

// Consumer gets type narrowing
function getStatusLabel(status: TaskStatus): string {
  switch (status.type) {
    case 'pending': return 'Pending';
    case 'in_progress': return `In progress (${status.assignee})`;
    case 'completed': return `Done on ${status.completedAt}`;
    case 'cancelled': return `Cancelled: ${status.reason}`;
  }
}
```

### Input/Output 离职

```typescript
// Input: what the caller provides
interface CreateTaskInput {
  title: string;
  description?: string;
}

// Output: what the system returns (includes server-generated fields)
interface Task {
  id: string;
  title: string;
  description: string | null;
  createdAt: Date;
  updatedAt: Date;
  createdBy: string;
}
```

### ID 使用品牌类型

```typescript
type TaskId = string & { readonly __brand: 'TaskId' };
type UserId = string & { readonly __brand: 'UserId' };

// Prevents accidentally passing a UserId where a TaskId is expected
function getTask(id: TaskId): Promise<Task> { ... }
```

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "我们会记录 API 以后再说吧 | 类型是文档,先定义它们。 |
| "我们现在不需要降级" |  一旦有人有100+项,你就会知道。从一开始就添加它。 |
| "PATCH很复杂,让我们用PUT" | PUT每次都需要完整对象. PATCH是客户真正想要的. |
| "我们会版本 API  当我们需要的时候" | 断开更改而无需断开版本 消费者。 设计从头开始扩展 。 |
| "没有人用那无证的行为" | Hyrum's Law:如果它是可观察到的,有人依赖它。将每一种公共行为视为一种承诺。 |
| "我们可以维持两个版本" |  多版本的维护成本倍增,并产生钻石依赖性问题. Prefer the One-Version Rule. |
| "内部API不需要合同" | 内部消费者仍然是消费者,合同防止连锁,并允许平行工作。 |
| "接受同上权 - 关键标题就够了" | 信头是合同; 存储结果的密钥是执行。 您接受但不尊重的密钥告诉客户重试时安全 。 |
| "我们的排队保证一交货" |  没有队列会穿过一个消费者崩溃——经纪人的Ack和你的副作用不在一个交易中. End-last-on with idepotentic processing. |
| "重复请求是罕见的" |  他们是 *关联* 在依赖性退化时,追索物会猛增——重复的瞬间最可能而且最昂贵。 |

## Red Flags


-  根据条件返回不同形状的终点
- 不同端点的不一致错误格式
- 验证分散在内部代码中而不是在边界中
- 打破对现有字段的更改 (类型变化,清除)
- 列出没有页标的结束点
- 动词在 REST URL( URL ) (`/api/createTask`, `/api/getUsers`)
- 第三方 API 未经验证或消毒而使用的回复
- A `SELECT` 单声道键,然后是 `INSERT` - 这是比赛,不是警卫
- 由 UUID 、 时间戳或每次尝试再生成的任何东西生成的偶效键
- 相同的密钥被不同的请求体接受,默默地返回第一个响应
-  一个比可重发送请求的最长路径短的密钥保留窗口

## Verification


设计一个 API:

- [ ]  每个端点都有输入和输出计划键入
- [ ] 采用单一统一格式的对错误的答复
- [ ] 只在系统边界进行校验
- [ ] 列表端点支持页化
- [ ]  新字段是添加剂和可选的 (向后兼容)
- [ ] 在所有终点上按照一致的公约命名
- [ ] API 与执行有关的文件或类型
- [ ] 改变状态的端点或遵守一元音键,或被记录为重试不安全
- [ ] 密钥在一次原子操作中声称 受到独特的制约
- [ ] 使用不同有效载荷的重用密钥会响亮失败, 而不是重放错误的响应
- [ ] 飞行中重复的反应是故意的选择 (409,等等,或202号) 而不是任何掉下来的东西
- [ ] 密钥保留超过最长的重试路径, 包括死字重放
