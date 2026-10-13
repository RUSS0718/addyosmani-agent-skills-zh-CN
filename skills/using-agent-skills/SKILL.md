---
name: using-agent-skills
description: Discovers and invokes agent skills. Use when starting a session, or when you need to decide which skill or workflow applies to the piece of work at hand. This is the meta-skill that governs how all other skills are discovered and invoked.
---

# 使用( E) Agent Skills

## Overview


Agent Skills 是一个由开发阶段组织起来的工程工作流程技能的集合。每个技能都编码了高级工程师遵循的特定进程。这种元技能帮助您为当前的任务发现和应用正确的技能。

## 技能发现

 当任务到达时,确定开发阶段并应用相应的技能:

```
Task arrives
    │
    ├── Don't know what you want yet? ──────→ interview-me
    ├── Have a rough concept, need variants? → idea-refine
    ├── New project/feature/change? ──→ spec-driven-development
    ├── No quality bar written down? ──→ constraint-driven-development
    ├── Have a spec, need tasks? ──────→ planning-and-task-breakdown
    ├── Implementing code? ────────────→ incremental-implementation
    │   ├── UI work? ─────────────────→ frontend-ui-engineering
    │   ├── API work? ────────────────→ api-and-interface-design
    │   ├── Need better context? ─────→ context-engineering
    │   ├── Need doc-verified code? ───→ source-driven-development
    │   └── Stakes high / unfamiliar code? ──→ doubt-driven-development
    ├── Writing/running tests? ────────→ test-driven-development
    │   └── Browser-based? ───────────→ browser-testing-with-devtools
    ├── Something broke? ──────────────→ debugging-and-error-recovery
    ├── Reviewing code? ───────────────→ code-review-and-quality
    │   ├── Too complex? ─────────────→ code-simplification
    │   ├── Security concerns? ───────→ security-and-hardening
    │   └── Performance concerns? ────→ performance-optimization
    ├── Committing/branching? ─────────→ git-workflow-and-versioning
    ├── CI/CD pipeline work? ──────────→ ci-cd-and-automation
    ├── Deprecating/migrating? ────────→ deprecation-and-migration
    ├── Writing docs/ADRs? ───────────→ documentation-and-adrs
    ├── Adding logs/metrics/alerts? ───→ observability-and-instrumentation
    └── Deploying/launching? ─────────→ shipping-and-launch
```

## 核心操作行为

这些行为在任何时候都适用,在各种技能中都是不可谈判的。

### 1. 地表假设

在执行任何非三角关系之前,明确表明你的假设:

```
ASSUMPTIONS I'M MAKING:
1. [assumption about requirements]
2. [assumption about architecture]
3. [assumption about scope]
→ Correct me now or I'll proceed with these.
```

 不要默默地填充模棱两可的要求,最常见的失败模式是作出错误的假设,并不加限制地运行,表面的不确定性很早——比重修便宜.

### 2. 积极管理困惑

 当遇到不一致、相互矛盾的要求或不明确的规格时:

1. **停下来。**  不要继续猜测。
2. 说出具体混淆.
3. 提出权衡或提出澄清问题。
4. 等待解决,然后继续。

**错误 :** 寂静地选择一个解释,希望这是正确的.
**不错:** "我看到X在规格中,但Y在现有的代码中,哪一个优先?"

### 3. 在获得许可时将后推

 你不是一个"是"机器,当一个方法有明显的问题时:

- 直接指出问题
- 解释一下混凝土的下坡 (可能时量化——"这增加了~200ms latency",而不是"这可能会更慢")
- 提出一个备选案文
- 接受人类的决定,如果他们用充分的信息推翻

Sycophancy是一种失败模式。 “当然! ” 之后是实施一个坏主意对任何人都没有帮助。 诚实的技术分歧比虚假的协议更有价值。

### 4. 强制执行简便性

 你的自然倾向是过于复杂,积极抵制.

 在完成任何执行之前,请:
- 能不能少说几句话?
-  这些抽象是获得其复杂性的吗?
-  一个员工工程师会看看这个 说"你为什么不..."吗?

 如果建造1000条线路和100条线路就足够了,你就失败了。更喜欢无聊的、显而易见的解决方案。聪明是昂贵的。

### 5. 维持范围纪律

 只抚汝所请抚汝.

不许 :
-  删除你不明白的评论
- “ 清除” 代码正对任务
- 将相邻系统重构为副作用
- 不经明确批准删除似乎未使用的代码
-  在光谱中添加不包含的特性, 因为它们“ 似乎有用 ”

 你的工作是手术精准, 不是主动的翻新。

### 6. 核实,不要假设

每种技能都包括核查步骤。在核查通过之前,一项任务是不完整的。“似乎正确”永远是不够的——必须有证据。 ( 通过测试, 构建输出, 运行时间数据).

技能验证是局部检查。适用于全项目的栏目 * 每张* 更改,无论哪些技能是活跃的,都是Done的定义:测试通过,没有回归,运行时行为得到核实,docs更新。 `../../references/definition-of-done.md` 它补充了每项任务的接受标准,而不是取代这些标准。

##  要避免的失败模式

 这些微妙的错误看起来像生产力,但却造成了问题:

1.  在不检查的情况下作出错误的假设
2.  不处理你自己的困惑——在迷路时继续前进
3.  不是浮现出你注意到的矛盾
4.  不对非明显决定作出权衡
5. 做独行侠 ("当然了!") 采取有明显问题的办法
6. 过错代码和 API
7.  为任务修改代码或注释
8. 清除你不明白的东西
9.  因为"很明显",所以没有字迹的建筑
10. 正在跳过校验, 因为“ 看起来正确 ”

## 技能规则

1. ** 在开工前检查是否具有适用技能 。** 技能编码过程可以防止常见错误.

2. **技能是工作流程,而不是建议。** 注意步骤,别跳过核查步骤

3. **多种技能可以应用.** 具体实施可能涉及: `idea-refine` → `spec-driven-development` → `planning-and-task-breakdown` → `incremental-implementation` → `test-driven-development` → `code-review-and-quality` → `code-simplification` → `shipping-and-launch` 以顺序排列。

4. ** 当有疑问时,从一个规格开始.**  如果任务是非三角的,没有谱号,请从 `spec-driven-development`.

## 生命周期序列

 对于一个完整的特征来说,典型的技巧序列是:

```
1.  interview-me                → Extract what the user actually wants
2.  idea-refine                 → Refine vague ideas
3.  spec-driven-development     → Define what we're building
4.  planning-and-task-breakdown → Break into verifiable chunks
5.  context-engineering         → Load the right context
6.  source-driven-development   → Verify against official docs
7.  incremental-implementation  → Build slice by slice
8.  observability-and-instrumentation → Instrument as you build (runs parallel with 7-9, not after)
9.  doubt-driven-development    → Cross-examine non-trivial decisions in-flight
10. test-driven-development     → Prove each slice works
11. code-review-and-quality     → Review before merge
12. code-simplification         → Reduce unnecessary complexity while preserving behavior
13. git-workflow-and-versioning → Clean commit history
14. documentation-and-adrs      → Document decisions
15. deprecation-and-migration   → Retire old systems and move users safely when needed
16. shipping-and-launch         → Deploy safely
```

并不是每个任务都需要各种技能。错误修复只需要: `debugging-and-error-recovery` → `test-driven-development` → `code-review-and-quality`.

## 快速引用

| 阶段 | 技能 |  一行摘要 |
|-------|-------|-----------------|
| 定义 | interview-me |  在任何计划、 规格或代码存在之前, 显示用户实际想要的 |
| 定义 | idea-refine |  通过结构多样和趋同的思维来完善想法 |
| 定义 | spec-driven-development | 代码前的要求和接受标准 |
| 计划 | planning-and-task-breakdown | 分解成可核查的小型任务 |
| 构建 | incremental-implementation | 细的垂直切片, 在扩展前测试每个切片 |
| 构建 | source-driven-development | 执行前对照官方文件进行核查 |
| 构建 | doubt-driven-development |  对每项非三边决定进行反面的新案文审查 |
| 构建 | context-engineering | 正确的上下文在正确的时间 |
| 构建 | frontend-ui-engineering | 生产质量 UI 具备无障碍条件 |
| 构建 | api-and-interface-design | 具有明确合同的稳定接口 |
| 校验 | test-driven-development |  先试失败,再通过 |
| 校验 | browser-testing-with-devtools | Chrome DevTools MCP 用于运行时间核查 |
| 校验 | debugging-and-error-recovery | 重复 —— 本地化 —— 固定 —— 警卫 |
|  审查 | code-review-and-quality | 带有质量门的五轴审查 |
|  审查 | code-simplification |  在减少不必要的复杂性的同时保留行为 |
|  审查 | security-and-hardening | OWASP 预防、输入验证、最小特权 |
|  审查 | performance-optimization |  先量一下,最优化的只是最重要的 |
| 船舶 | git-workflow-and-versioning | 原子犯罪 干净的历史 |
| 船舶 | ci-cd-and-automation |  每次更改时自动设置质量门 |
| 船舶 | deprecation-and-migration |  删除旧系统并安全迁移用户 |
| 船舶 | documentation-and-adrs | 记录原因,不只是什么 |
| 船舶 | observability-and-instrumentation | 结构化日志、RED指标、跟踪、症状警报 |
| 船舶 | shipping-and-launch | 发射前核对表、监测、后退计划 |
