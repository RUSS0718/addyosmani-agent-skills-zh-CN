---
name: observability-and-instrumentation
description: Instruments code so production behavior is visible and diagnosable. Use when adding logging, metrics, tracing, or alerting. Use when shipping any feature that runs in production and you need evidence it works. Use when production issues are reported but you can't tell what happened from the available data.
---

# 观察和仪表

## Overview


无法观察的代码是无法操作的代码,可观察性是能够从外部回答"系统在做什么和为什么?",使用代码的遥测,仪器不是发射后添加的——它和特性一起写,同样的测试方式,如果一个没有遥测的特征船,第一个用户报告的bug变成了考古学而不是一个查询.

## When to Use


-  * 建立生产中的任何特征
- 添加新的服务、终点、背景工作或外部整合
- 生产事件花了太长时间才诊断出来 ("我们不知道发生了什么")
- 制定或审查警报规则
-  审查添加内容的公关 I/O,重复、排队或交叉服务呼叫

** 不为:**
- 诊断一下目前发生的失败——使用 `debugging-and-error-recovery` 技能 (观察力是它使 技能在下次快速)
- 分析并优化所测慢度——使用 `performance-optimization` 技能
- 发射日监测清单和回滚触发器——见 `shipping-and-launch` 技能;这种技能涵盖供养他们的仪器

## 进程

### 1. 在仪器制作前定义“工作”

 没有问题的遥测是噪音。在添加任何仪器之前,请写下2至4个问题。

```
FEATURE: checkout payment retry
QUESTIONS ON-CALL WILL ASK:
1. What fraction of payments succeed on first attempt vs after retry?
2. When a payment fails permanently, why? (provider error? timeout? validation?)
3. Is the payment provider slower than usual?
→ Every signal below must help answer one of these.
```

 如果你不能点出问题的名字,你还没有准备好乐器——你会记录一切,学习什么也没有.

### 2. 为每个问题选择正确的信号

| 信号 | 答复 | 费用概况 | 示例 |
|---|---|---|---|
| **结构化日志** | "这具体案子到底发生了什么?" |  每件活动;随交通量增长 | `payment_failed` 带有提供者错误代码 |
| **度量衡** | "几度/多快,总和?" |  每个序列固定; 查询便宜 | p99 提供者呼叫的延迟性 |
| **追踪** | "时间从哪里跨过服务?" |  按请求; 通常为样本 |  一个慢退房,按跳拆分 |

拇指规则: 度量衡告诉你 ** 那个**  有些事不对劲, 痕迹告诉你 **地点**,日志告诉你 ** 为什么**.

### 3. 结构化伐木

日志事件,不是传言。每个日志行都是 JSON 具有稳定事件名称和机器可读字段的对象:

```typescript
// BAD: string interpolation — unqueryable, inconsistent
logger.info(`Payment ${id} failed for user ${userId} after ${n} retries`);

// GOOD: stable event name + structured fields
logger.warn({
  event: 'payment_failed',
  paymentId: id,
  provider: 'stripe',
  errorCode: err.code,
  attempt: n,
}, 'payment failed');
```

**日志级别——一致使用:**

| 职等 | 含义 | 待命行动 |
|---|---|---|
| `error` | 断断续续;可能需要有人采取行动 | 调查 |
| `warn` | 有辱人格但经处理 (重试成功, 使用倒计时) | 注意趋势 |
| `info` | 重大商务活动 (命令已放置, 任务已完成) |  无 |
| `debug` | 诊断细节 | 默认停产 |

**相关身份证明是强制性的。** 生成 (或接受) 请求 ID 在系统边界上附加到每个日志行、 跨度和外出调用中。 没有它, 您无法重建来自 Interleaved 日志的单个请求 :

```typescript
// Express: child logger per request, ID propagated downstream
app.use((req, res, next) => {
  req.id = req.headers['x-request-id'] ?? crypto.randomUUID();
  req.log = logger.child({ requestId: req.id });
  res.setHeader('x-request-id', req.id);
  next();
});
```

** 当多个切入点写到一个日志时,请命名切入点.** 关联 ID 识别运行; 它没有说明启动该运行的代码路径。 调度器、 重播端点和手动操作达到的相同任务 。 CLI 运行在一个槽中生成可互换的线条, 因此将一条线归结为消除—— 交叉读取调度器的历史, 进程表, 部署日志 —— 而这个参数只保留到这些外部记录还存在时。 标记运行开始的入口, 在相关 ID 旁边, 并同时传播 :

```typescript
// One helper for every entry point: the run's own logger carries both fields.
// `entryPoint`, not `source` — ECS reserves `source.*` for network fields.
export const runLog = (entryPoint: 'scheduler' | 'replay_endpoint' | 'cli', runId: string) =>
  logger.child({ entryPoint, requestId: runId });

// scheduler tick        -> runLog('scheduler', crypto.randomUUID())
// POST /jobs/:id/replay -> runLog('replay_endpoint', req.id)
// CLI invocation        -> runLog('cli', process.env.RUN_ID ?? crypto.randomUUID())
```

 两个字段必须跨越和关联ID相同的边界——队列元数据, HTTP 标题 — — 或工人重新思考切入点和猜想。 仅仅与切入点相关联的字段是一个提示,而不是一个归属:任何能引用该工作的东西都可以复制它。

**永远不要登录秘密、信物、密码或完整PII。**  这是很难的规矩 `security-and-hardening` 技能 —— 遥测管道是一个经典的数据漏流路径 。 允许列表字段; 不要登录整个请求机构 。

### 4. 计量

 关于由请求驱动的服务,工具 **红色**  每个终点和每个外部依赖: **R**吃过 (requests/sec), **E**错误 (失败率), **D** 小便费 (直方图,不平均)资源 (队列、 池、 主机),使用 **用户**: **U**蒸馏, **S**尿液, **E**讨厌。

 同追踪一样,供应商中立的途径是: OpenTelemetry 计量 API (不变 SDK 和背景作为步骤5)。下面的例子使用 Prometheus 。 `prom-client` - 一个常见的后端选择,而不是唯一的选择; RED/USE 至高无上的规则是完全相同的。

```typescript
import { Histogram } from 'prom-client';

const httpDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration',
  labelNames: ['method', 'route', 'status_class'],  // '2xx', not '200'
  buckets: [0.05, 0.1, 0.25, 0.5, 1, 2.5, 5],
});
```

**红衣主教制是失败模式.**  每个独特的标签组合都是单独的时序。标签必须来自小的、固定的集 (路由模板、状态类、提供者名称)。从不使用用户ID,原始URL,错误消息,或其他未绑定的值作为标签——属于日志和痕迹.

```
OK as label:    route="/api/tasks/:id"   status_class="5xx"   provider="stripe"
NEVER a label:  user_id, email, request_id, full URL, error message text
```

音轨平均值,百分位总是: 平均隐藏1% 的用户有糟糕的时间。 使用直方图并读取 p50/p95/p99.

### 5. 分布式追踪

使用( U) OpenTelemetry - 这是供应商中立的标准, 和自动仪器封面 HTTP, gRPC, 和具有近零码的常见 DB 客户端 :

```typescript
// tracing.ts — must be imported before anything else
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';

const sdk = new NodeSDK({
  serviceName: 'checkout-service',
  instrumentations: [getNodeAutoInstrumentations()],
});
sdk.start();
```

仅围绕有意义的内部工作单位添加手动跨度 ( 例如, `applyDiscounts`, `chargeProvider`) 并附加调用属性将过滤。 跨越每个同步边界的上下文 。 HTTP 标题, 队列消息元数据 – 或微量在空白处死亡 。 默认情况下, 样本头基于低速; 如果您的后端支持尾端采样, 则保留100% 的错误 。

### 6. 警示

警报 **症状使用者感觉**,不针对原因:

```
SYMPTOM (page-worthy):           CAUSE (dashboard, not a page):
error rate > 1% for 5 min        CPU at 85%
p99 latency > 2s                 one pod restarted
queue age > 10 min               disk at 70%
```

 基于原因的警报在无事可做时会起火,并错过你没有预测的故障。基于症状的警报在用户受伤时,无论原因为何,都完全会起火。

 您创建的每个提醒规则 :

1. ** 它必须是可采取行动的。**  如果反应是"不光彩,自愈",则删除警示.
2. ** 它链接到运行本** ——甚至三行:意思是什么,第一个查询运行,升级路径.
3. ** 它有一个门槛和持续时间** 由SLO或历史数据证明,而不是通过猜测证明。
4.  只使用两个断层 : ** 页 次** (显示用户身份, 现在就行动) 财务报告和财务报告 **门票** (堕落,本周行动) 第三层变成噪音 训练人们忽略一切

#### 写入运行簿

以上规则2要求每一次提醒都要链接到一个运行本. 运行本的工作是回答三个问题,而不要求读者思考:正在发生什么,首先检查什么,如果无法解决,谁可以打电话. `docs/runbooks/` 以提醒方式命名。

**最小可行的运行本 ( 三行线):**

```markdown
# Runbook: High Error Rate on /api/tasks
**Means:** DB connection pool likely exhausted, or a bad deploy.
**First check:** `SELECT count(*) FROM pg_stat_activity WHERE backend_type = 'client backend';`
  — if count > pool limit, see Step 2. (Swap in the equivalent for your database.)
**Escalate to:** #db-oncall or engineering on-call rotation.
```

**何时扩展至三行之外:** 仅当第一次检查不足以决定时,才会添加步骤。一个涵盖三个最常见的原因的五步运行本比一个涵盖每个边缘情况并被滑动的二十步文档要好。

**保持运行簿流畅.** 更新运行本, 作为关闭每一起事件的一部分 —— 一个 stale 运行本会建立虚假的信心。 如果一个步骤错误或缺失, 请在标记事件解决之前修好它 。

### 7. 校验遥测本身

仪器是代码; 它可能是错误的。 在调用已完成的工作之前, 触发路径并查看实际输出 :

- 强制在运行中出错 {} 在日志中找到它 。 `requestId`,确认字段的结构 ( 没有 `[object Object]`)
- 发送测试流量 = 确认参数序列 与预期标签和正常值一起出现
-  在追查过程中,按照一项服务要求行事 UI - 没有断裂的跨度
- 发射每个新警报一次 (暂时降低门槛值)  确认它到达了正确的频道 和运行本链接的工作

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "在伐木成功后,我会增加伐木量" | "之后"成为"第一次事件之后",这是发现你失明的最昂贵的时刻,就像你建造的仪器一样. |
| "更多日志 = 更可观察" |  无结构噪声让事件变慢,而不是变快. 3个可查询事件击败了300条散文线. |
| "Console.log现在还好" | 结构化的输出不能被过滤、关联或提醒。结构化的日志机需要5分钟的额外时间。 |
| "我们只要看到仪表盘 当有东西打破" |  在没有定义的提问中建造的答覆板会显示除答案以外的一切。从待命提问开始 。 @ info: status |
| "一切重要的事情,我们待会再调音" |  一个吵闹的寻呼器会训练人们忽略它。调音永远不会发生;错失了真实的页面会发生。 |
| "用人单位ID作为标语标签 方便调试" | 高心率检查属于日志和痕迹。 |
| "追踪对我们两个服务来说是过度杀戮" |  两个服务已经意味着跨服务空档问题日志无法回答,自动仪器使得成本变得微不足道. |

## Red Flags


- 带有重试、队列或外部调用和零新遥测功能的特性 PR
- 由字符串内插而不是结构化字段构建的日志行
-  没有 correlation/request ID——每个日志行都是孤儿
- 一个由调度器、 Webhook 和手动运行输入的日志流, 没有字段命名 。
- 标签为用户标识、原始 URL 或错误消息文本的计量 (基本炸弹)
-  无百分位数的延迟跟踪为平均值
- 日复一日地发火,毫无行动地被认出来
-  关于原因的警告 (CPU 内存)  在未监测到用户显示错误率时呼唤人类
- 日志中出现的秘密、标志或完整的请求机构
- "它在我的机器上工作" 作为唯一的证据 一个生产特征是健康的

## Verification


 在对特性进行仪器处理后,确认:

- [ ]  这个功能的待召问题写下来,每个信号地图都写到一个
- [ ]  所有日志输出结构化 (JSON),每行都有稳定事件名称和关联ID
- [ ] 由一个以上入口撰写的每个日志槽都包含一个切入点字段,设定运行的起始位置并用相关ID进行传播,而不是在下游推断
- [ ]  在任何日志行中都没有秘密、标志或未编辑的 PII (抽查实际产出)
- [ ] RED 度量衡适用于每个新的终点和每个外部依赖,并有捆绑的标签集
- [ ] 纬度是直方图; p95/p99  可查询
- [ ] 追踪中可遵循单一请求的端对端 UI  没有断裂的跨度
- [ ] 每一个新的警报都是基于症状的,有一个运行本链接, 并被试射过一次
- [ ] 仅通过遥测就发现中转发生故障,而未读取源头

 本列表的亮度版本,包括发射前的仪表门,参见: `../../references/observability-checklist.md`.
