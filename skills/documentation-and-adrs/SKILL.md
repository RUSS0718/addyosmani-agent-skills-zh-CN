---
name: documentation-and-adrs
description: Records decisions and documentation. Use when you need to document an architecture decision (ADR) or the reasoning behind a design choice, when changing public APIs, shipping features, or when you need to record context that future engineers and agents will need to understand the codebase.
---

# 文件和发展成果评估

## Overview


 文件决定,而不仅仅是代码。 * 为什么* 导致作出决定的背景、限制和权衡。 *怎么样?* 已建; 文档解释 * 为什么它是这样建造的* 财务报告和财务报告 *考虑了哪些替代品* 这种背景对于未来在密码库工作的人类和代理人至关重要。

## When to Use


- 做出重大建筑决定
-  在相互竞争的办法之间作出选择
- 增加或改变公众 API
- 传输一个改变用户行为特性
- 接纳新的小组成员 (或代理人)  对项目的建议
-  当你发现自己 一再解释同样的事情

** 不使用时 :**  不要记录明显的代码。 不要添加重述代码已经表达的意见。 不要为丢弃的原型写文件 。

## 结构决定记录 (发展成果评估)

ADRs 捕捉了重要技术决策背后的推理,是您能够写出的最高价值的文档.

### 何时写作 ADR

- 选择框架、库或主要依赖性
- 设计数据模型或数据库计划
- 选择认证策略
- 在一个 API 结构 (REST 与《公约》第2条的关系 GraphQL 诉土耳其人权委员会)
-  在构建工具、托管平台或基础设施之间进行选择
- 任何代价高昂的决定

###  首先与现行公约相匹配

 在创建前 ADR,检查既定公约——现有发展成果评估、项目指示和 ADR- 相关配置或工具 ( 例如, `.adr-dir`  文件)。既定的公约优先于下面的默认值。匹配 :

- **地点和格式**  比如说。 `docs/adr/*.md`, `Documentation/Decisions/*.rst`, MADR布局,或 `adr-tools` 设置。匹配现有的目录、文件扩展名和标记 (Markdown vs reStructured 图文).
- **编号和命名** 继续现有的序列和文件名模式 (`ADR-004-Title.rst`, `0004-title.md`, …); 不要在 001 重新开始或引入第二个方案。
- **节标题** ——重用项目的标题集,而不是强制使用本模板.

 如果现有证据有冲突,那么就直面冲突,而不是默默地引入另一个方案。只有在无法确立任何公约时,你才适用下面的默认。

### ADR 模板

将 ADRs 存储到 `docs/decisions/` 带有顺序编号 ( 除非项目已经使用其他地点——见上文。):

```markdown
# ADR-001: Use PostgreSQL for primary database

## Status
Proposed | Accepted | Superseded by ADR-XXX | Deprecated

## Date
2025-01-15

## Context
We need a primary database for the task management application. Key requirements:
- Relational data model (users, tasks, teams with relationships)
- ACID transactions for task state changes
- Support for full-text search on task content
- Managed hosting available (for small team, limited ops capacity)

## Decision
Use PostgreSQL with Prisma ORM.

## Alternatives Considered

### MongoDB
- Pros: Flexible schema, easy to start with
- Cons: Our data is inherently relational; would need to manage relationships manually
- Rejected: Relational data in a document store leads to complex joins or data duplication

### SQLite
- Pros: Zero configuration, embedded, fast for reads
- Cons: Limited concurrent write support, no managed hosting for production
- Rejected: Not suitable for multi-user web application in production

### MySQL
- Pros: Mature, widely supported
- Cons: PostgreSQL has better JSON support, full-text search, and ecosystem tooling
- Rejected: PostgreSQL is the better fit for our feature requirements

## Consequences
- Prisma provides type-safe database access and migration management
- We can use PostgreSQL's full-text search instead of adding Elasticsearch
- Team needs PostgreSQL knowledge (standard skill, low risk)
- Hosting on managed service (Supabase, Neon, or RDS)
```

### ADR 寿命周期

```
PROPOSED → ACCEPTED → (SUPERSEDED or DEPRECATED)
```

- ** 不要删除旧的 ADRs 。**  他们抓住了历史背景。
-  当决定更改时, 写一个新的 ADR 引用并取代了旧的。

## 内置文档

### 何时注释

评论 * 为什么*,而不是从 *怎么样?*:

```typescript
// BAD: Restates the code
// Increment counter by 1
counter += 1;

// GOOD: Explains non-obvious intent
// Rate limit uses a sliding window — reset counter at window boundary,
// not on a fixed schedule, to prevent burst attacks at window edges
if (now - windowStart > WINDOW_SIZE_MS) {
  counter = 0;
  windowStart = now;
}
```

###  当不备注时

```typescript
// Don't comment self-explanatory code
function calculateTotal(items: CartItem[]): number {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}

// Don't leave TODO comments for things you should just do now
// TODO: add error handling  ← Just add it

// Don't leave commented-out code
// const oldImplementation = () => { ... }  ← Delete it, git has history
```

### 已知的文档

```typescript
/**
 * IMPORTANT: This function must be called before the first render.
 * If called after hydration, it causes a flash of unstyled content
 * because the theme context isn't available during SSR.
 *
 * See ADR-003 for the full design rationale.
 */
export function initializeTheme(theme: Theme): void {
  // ...
}
```

## API 文档

用于公共 API (REST, GraphQL,库接口):

### 内置类型 (首选 TypeScript)

```typescript
/**
 * Creates a new task.
 *
 * @param input - Task creation data (title required, description optional)
 * @returns The created task with server-generated ID and timestamps
 * @throws {ValidationError} If title is empty or exceeds 200 characters
 * @throws {AuthenticationError} If the user is not authenticated
 *
 * @example
 * const task = await createTask({ title: 'Buy groceries' });
 * console.log(task.id); // "task_abc123"
 */
export async function createTask(input: CreateTaskInput): Promise<Task> {
  // ...
}
```

### OpenAPI / Swagger 为 REST APIs 辅助程序

```yaml
paths:
  /api/tasks:
    post:
      summary: Create a task
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateTaskInput'
      responses:
        '201':
          description: Task created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '422':
          description: Validation error
```

## README 结构

 每个项目都应有一个包含以下内容的README:

```markdown
# Project Name

One-paragraph description of what this project does.

## Quick Start
1. Clone the repo
2. Install dependencies: `npm install`
3. Set up environment: `cp .env.example .env`
4. Run the dev server: `npm run dev`

## Commands
| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server |
| `npm test` | Run tests |
| `npm run build` | Production build |
| `npm run lint` | Run linter |

## Architecture
Brief overview of the project structure and key design decisions.
Link to ADRs for details.

## Contributing
How to contribute, coding standards, PR process.
```

## Changelog 维护

 对于已发运的特性 :

```markdown
# Changelog

## [1.2.0] - 2025-01-20
### Added
- Task sharing: users can share tasks with team members (#123)
- Email notifications for task assignments (#124)

### Fixed
- Duplicate tasks appearing when rapidly clicking create button (#125)

### Changed
- Task list now loads 50 items per page (was 20) for better UX (#126)
```

##  为代理人提供的文件

 对人工智能代理人背景的特殊考虑:

- **CLAUDE.md / 规则文件** - 记录项目公约,使代理人遵循这些公约
- **光谱文件** ——不断更新规格,使代理商能够建立正确的东西.
- **发展成果评估** - 帮助代理人了解过去为何作出决定; (防止重新决定)
- **内在获取** 防止特工落入已知的陷阱

## Prose审查

 在检查技术内容后,要为使用该内容的人审查该词的流派,更清晰的措辞不得改变决定或限制.

1. 标记编辑时必须幸存的细节:名称、数字和单位、命令、路径、链接、条件、权衡和不确定性。对照可用的代码和决定上下文来检查这些细节;标记您无法证实的任何东西。
2. 将库存介绍、促销形容词和重复摘要替换为读者需要的具体信息。不要用发明的示例或更强的保证来取代无支持的主张。请保留必要的技术重复和项目的既定术语。
3. 将编辑的文档与原始文档及其来源进行比较。 恢复任何丢失的限定词或提示, 验证命令和代码示例仍然有效, 并解释任何未解决的事实问题, 而不是擦除它 。

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "密码是自制文件" | 代码显示什么,它没有显示为什么, 哪些替代品被拒绝, 或有哪些限制。 |
| "我们会写文件,当 API 稳定" | API 记录它们时稳定得更快。 Doc 是设计的第一个测试 。 |
| "没有人读文件" | 特工有,未来工程师有,你三个月后自己有 |
| "ADRs是头顶" | 10分钟 ADR 防止六个月后对同一决定进行两小时的辩论。 |
| "评论变得过时" | 评论 * 为什么* 稳定。 *怎么样?* 变得过时了,这就是为什么你只写前者。 |

## Red Flags


-  无书面理由的建筑决定
-  没有文档或类型的公共 API
- 无法解释如何运行工程的 README
- 注释出代码而不是删除
- TODO 已存在数周的评论
-  在具有重大建筑选择的项目中没有ADRs
- 重述代码而不是解释意图的文档

## Verification


记录后:

- [ ]  所有重大建筑决定都存在发展成果评估
- [ ] README 涵盖快速启动、命令和架构概览
- [ ] API 函数有参数和返回类型的文档
- [ ] 已知的查查被记录在他们重要之处
- [ ]  没有留下已评论的退出代码
- [ ] 规则文件 (CLAUDE.MD 等 (英语).)  是当前和准确的
- [ ] Prose 编辑保存源代码细节,决定权衡,以及限定符,而不添加无支持的保证
