# 翻译术语表

本表统一中文版文档中的核心译法。代码、文件名、skill 名称、命令和 API 标识符保持英文原文。

原仓库：<https://github.com/addyosmani/agent-skills>

| English term | 中文译法 | 说明/语境 |
|---|---|---|
| Agent Skills | Agent Skills（代理技能） | 保留项目名称；指可复用的 AI 代理工作流。 |
| AI coding agent | AI 编码代理 | 能读取代码、调用工具并执行开发任务的 AI。 |
| skill | 技能 | 带有触发条件、步骤和退出标准的工作流文档；目录名和 `SKILL.md` 保持不变。 |
| workflow | 工作流 | 可重复执行的一组步骤、检查点和产出。 |
| prompt | 提示词 | 发送给模型的任务说明或输入。 |
| context engineering | 上下文工程 | 组织规则、背景和输入，使代理在当前任务中获得正确上下文。 |
| specification / spec | 规格 / 规范 | 编码前明确目标、范围、约束和验收方式的文档。命令 `/spec` 保持不变。 |
| requirements | 需求 | 用户或系统必须满足的能力、约束和行为。 |
| acceptance criteria | 验收标准 | 判断任务完成与否的可观察条件。 |
| test-driven development (TDD) | 测试驱动开发（TDD） | 先写失败测试，再实现，再重构；保留缩写 TDD。 |
| red-green-refactor | 红—绿—重构 | TDD 循环：测试失败、通过、整理实现。 |
| code review | 代码审查 | 对变更的正确性、风险、规范和可维护性进行审阅。 |
| architecture decision record (ADR) | 架构决策记录（ADR） | 记录架构背景、决策、取舍和后果的文档。 |
| API | API / 应用程序接口 | 与外部调用者或模块约定的接口；保留 API。 |
| interface | 接口 | 模块、服务或组件之间可调用的边界。 |
| contract | 契约 | 对输入、输出、错误和兼容性的明确约定。 |
| endpoint | 端点 | API 中可访问的具体 URL 和方法组合。 |
| schema | 模式 / Schema | 数据、API 或配置的结构定义；涉及代码时可保留 Schema。 |
| module boundary | 模块边界 | 模块职责和依赖关系的清晰分界。 |
| deprecation | 弃用 | 宣布旧接口或能力不再推荐使用，并准备迁移路径。 |
| migration | 迁移 | 从旧实现、数据结构或流程转移到新实现。 |
| observability | 可观测性 | 通过日志、指标和 trace 了解系统运行状态的能力。 |
| telemetry | 遥测数据 | 系统运行时产生的日志、指标、追踪和事件数据。 |
| continuous integration / continuous delivery (CI/CD) | 持续集成 / 持续交付（CI/CD） | 自动构建、校验、发布的软件交付流程。 |
| feature flag | 功能开关 | 在不重新部署的情况下控制功能开放范围的开关。 |
| rollback | 回滚 | 将发布或变更恢复到已知稳定版本。 |
| release | 发布 | 将某个可复现版本交付给使用者。 |
| trunk-based development | 主干开发 | 以短生命周期分支和频繁合并为特征的 Git 协作方式。 |
| worktree | 工作树 | Git 同一仓库中并行检出多个工作目录的机制。 |
| fixture | 测试夹具 | 为测试提供的固定输入、文件或环境。 |
| eval / evaluation | 评测 | 用预设案例检查技能触发、路由或行为质量。 |
| trigger | 触发条件 | 判断某个 skill 或流程应该启动的输入特征。 |
| routing | 路由 | 将任务分配给合适的 skill、代理或处理流程。 |
| verification | 验证 | 用测试、构建结果、运行时证据或人工检查证明结果成立。 |
| red flag | 红旗信号 | 表示流程可能被错误应用或质量正在下降的迹象。 |
| rationalization | 合理化借口 | 代理用来跳过必要步骤的常见理由及其反驳。 |
| deep module | 深模块 | 以小而稳定的接口封装较多实现细节的模块。 |
| idempotency | 幂等性 | 同一意图重复执行不会产生额外副作用的性质。 |
| OWASP | OWASP | Web 安全风险与实践的权威社区项目名称。 |
| Core Web Vitals | Core Web Vitals（核心 Web 指标） | 衡量真实用户 Web 性能的 Google 指标集合。 |
| MCP | MCP | Model Context Protocol 的缩写，保留英文缩写。 |
| YAML frontmatter | YAML 前置元数据 | Markdown 文件开头由 `---` 包围的结构化元数据。 |

## 译法约定

- 目录名、文件名、命令、代码标识符、URL 和 API 字段保持英文，避免复制后失效。
- 已成为行业惯例的缩写（API、CLI、CI/CD、TDD、ADR、MCP 等）保留英文缩写，并在首次出现时给出中文解释。
- 同一术语在同一语境中使用同一译法；如果中文会掩盖具体实现含义，则保留英文并补充中文说明。
