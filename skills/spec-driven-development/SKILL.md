---
name: spec-driven-development
description: Creates specs before coding. Use when starting a new project, feature, or significant change and no specification exists yet. Use when drafting a PRD or requirements document with objectives and scope, or when requirements are unclear, ambiguous, or only exist as a vague idea. Use when a single requirement spans several independently testable capabilities and needs decomposing into a capability map of modules before specifying.
---

# 光谱驱动开发

## Overview


 在写代码之前,先写一个结构化的规格,这个规格是你们和人类工程师之间共同的真理来源——它定义了我们正在建造的东西,为什么,以及我们将如何知道它是如何完成的,没有规格的密码就是猜测.

## When to Use


- 启动新项目或功能
- 要求不明确或不完整
- 更改触及多个文件或模块
-  你即将做出一个建筑决定
- 执行这项任务需要30分钟以上的时间。

** 不使用时 :** 单行校正、类型校正,或要求明确且自成一体的改动。

## Gate 工作流程

光谱驱动开发分四个阶段,先进行范围检查. (阶段 0)  当一个请求捆绑了几种独立的可测试能力时,才会激活。在验证当前状态之前,不要推进到下一个阶段。

```
SPECIFY ──→ PLAN ──→ TASKS ──→ IMPLEMENT
   │          │        │          │
   ▼          ▼        ▼          ▼
 Human      Human    Human      Human
 reviews    reviews  reviews    reviews
```

###  第0阶段:范围检查

多数请求都描述了一种能力。 如果有, 请跳过此阶段, 直接指定 。 0 阶段为例外而非规则存在, 它没有给单能力特性设置分级 。

**侦测**  在指定单项要求何时捆绑若干可独立测试的能力之前进行分解:

- 要求列出了与自己的消费者或数据有关的不同能力 ( 例如,身份、账单、通知、报告)
- 将接受标准分组为可单独运送和核查的类别
- 一种能力可以在不重写其他能力要求的情况下被削减或替换

** 在写入任何规格之前, 提出一个能力图 。** 小型和可审查的——模块表加上建设顺序,而不是项目计划:

```markdown
# Capability Map: [Initiative Name]

| Module id | Responsibility | Depends on |
|---|---|---|
| identity | Accounts, sessions, SSO | — |
| billing | Plans, invoices, payments | identity |
| notifications | Email and webhook fan-out | identity |
| reporting | Usage dashboards | billing, notifications |

Build order: identity → billing, notifications → reporting
```

- **稳定模块编号。** Kebab- case, 选择过一次, 从未重新命名为 中间主动性 。 光谱、 计划、 下游命令用这些 ID 来选择工作, 而不是猜测哪个 spec 是活跃的 。
- **依赖方向,无循环.** 箭头指向一个方向。如果每个模块需要两个模块,它们就是一个模块。
- **界面位于边界。** 地图上记录到 `billing` 取决于 `identity`;它们之间的合同属于供应商模块的光谱 ( 见 `api-and-interface-design` 用来设计它).

**地图的门像每个阶段一样。** 人类评论模块的边界,依赖方向,以及任何模块光谱写入前的构建秩序。让地图错误是昂贵的;审查十行不是。

** 然后重复每个模块。** 运行指定 → 计划 → 任务 → 按依附顺序执行每个模块。 每个模块都获得自己的光谱, 范围与该模块的目标、 界限和成功标准相符 。 请将批准的地图保存在项目根部以及每个模块的光谱旁边, 以模块 ID 命名 。 (`SPEC-identity.md`, `SPEC-billing.md`) ——地图,而非文件名猜测,是存在的索引.

### 阶段1:指定

 先从高层次的视野开始,问人类澄清问题,直到要求具体化.

**立即进行表面假设。**  在撰写任何光谱内容之前,请列出您所假设的内容:

```
ASSUMPTIONS I'M MAKING:
1. This is a web application (not native mobile)
2. Authentication uses session-based cookies (not JWT)
3. The database is PostgreSQL (based on existing Prisma schema)
4. We're targeting modern browsers only (no IE11)
→ Correct me now or I'll proceed with these.
```

 别无声无息地填充模糊的要求,光谱的全部目的就是表露误解 *在此之前* 代码被写入——假设是最危险的误解形式.

**写一个涵盖这六个核心领域的光谱文件:**

1. **目标**  我们在建什么?谁是用户?成功是什么样子?

2. **图标** ——带有旗帜的完整可执行命令,而不仅仅是工具名称.
   ```
   Build: npm run build
   Test: npm test -- --coverage
   Lint: npm run lint --fix
   Dev: npm run dev
   ```

3. ** 项目结构** ——源代码存在的地方,测试去的地方,docs所属的地方.
   ```
   src/           → Application source code
   src/components → React components
   src/lib        → Shared utilities
   tests/         → Unit and integration tests
   e2e/           → End-to-end tests
   docs/          → Documentation
   ```

4. **代码样式** —— 一个显示您风格的真正的代码片段比三段描述它。 包括命名常规、 格式化规则和良好输出的例子 。

5. **测试战略** 测试所在的什么框架,覆盖的预期,以及值得关注的测试水平。

6. **边界** 三级系统:
   - **总是如此:**  在承诺前进行测试,遵循命名惯例,验证输入
   - ** 先问:** 数据库计划更改, 添加依赖性, 更改 CI 配置
   - **从来没有:** 提交秘密, 编辑供应商目录, 不经批准删除失败的测试

**光谱模板 :**

```markdown
# Spec: [Project/Feature Name]

## Objective
[What we're building and why. User stories or acceptance criteria.]

## Tech Stack
[Framework, language, key dependencies with versions]

## Commands
[Build, test, lint, dev — full commands]

## Project Structure
[Directory layout with descriptions]

## Code Style
[Example snippet + key conventions]

## Testing Strategy
[Framework, test locations, coverage requirements, test levels]

## Boundaries
- Always: [...]
- Ask first: [...]
- Never: [...]

## Success Criteria
[How we'll know this is done — specific, testable conditions]

## Open Questions
[Anything unresolved that needs human input]
```

**外部光谱工具 :**  此工作流程是格式不可知的。 如果工程
已使用 OpenSpec 或其他规格系统, 保留系统的
工艺品格式和储存惯例,而不是制造复制品
`SPEC.md`. 这一技能拥有澄清、内容和批准大门;
外部工具拥有已核准的 spec 表达方式 。

**重新制定指令作为成功标准。** 在收到模糊要求时,将其转化为具体条件:

```
REQUIREMENT: "Make the dashboard faster"

REFRAMED SUCCESS CRITERIA:
- Dashboard LCP < 2.5s on 4G connection
- Initial data load completes in < 500ms
- No layout shift during load (CLS < 0.1)
→ Are these the right targets?
```

 这样你就可以循环、重试和解决问题,实现一个明确的目标,而不是猜测“更快”意味着什么。

**写入光谱后停止 (晶体).**  一旦 spec 被保存 :

1. 总结并列出任何开放问题。
2. 要求人类批准或要求修改.
3. **停止你的转身 及时。**  不启动第2阶段, 引用 `planning-and-task-breakdown`,或者将代码写在这个转弯处。计划只有在人类批准后才能开始。

### 第二阶段:计划

 在经过验证的基础上,制定技术执行计划:

1. 确定主要组成部分及其依赖性
2. 确定执行令 (一定要先建起来的东西)
3. 备注风险和缓解战略
4. 确定可平行构建的与必须依次构建的
5. 确定各阶段之间的核查检查站

> 跟着 `planning-and-task-breakdown`  对于这些步骤背后的依赖图图和垂直切换力学,这是犬科的源头。上面的子弹是一个轻量级的总结;如果它们有任何偏差, `planning-and-task-breakdown` 以优先为准。
>
> **产出会议:**  把计划保存到 `tasks/plan.md` 并记录任务列表中的任务列表目标 `planning-and-task-breakdown` (默认 `tasks/todo.md`;项目可以指定一个外部跟踪器)创建 `tasks/`  如果不存在的话。下游命令 (`/build`, 等 (简体中文).) 期望这些默认值。

 该计划应该可以审查:人类应该能够阅读,并说"是的,这是正确的方法"或"不,改变X".

###  第3阶段:任务

将计划分为分散的、可执行的任务:

- 每项任务应在一次有重点的会议上完成
- 每项任务都有明确的接受标准
- 每项任务包括核查步骤 (测试, 构建, 手动检查)
- 任务是依附性而不是视其重要性而定的
- 任何任务都不应要求更改多于~5文件

> 跟着 `planning-and-task-breakdown`  下面的模板是一个轻量级的内线形式;如果它们有分歧, `planning-and-task-breakdown` 以优先为准。

**任务模板 :**
```markdown
- [ ] Task: [Description]
  - Acceptance: [What must be true when done]
  - Verify: [How to confirm — test command, build, manual check]
  - Files: [Which files will be touched]
```

### 阶段4:实施

之后一次执行一个任务 `skills/incremental-implementation/SKILL.md` (`incremental-implementation`) 财务报告和财务报告 `skills/test-driven-development/SKILL.md` (`test-driven-development`)组合键。 `skills/context-engineering/SKILL.md` (`context-engineering`) 在每个步骤上加载正确的光谱区段和源文件,而不是将整个光谱淹没给代理。

## 保持斯佩克的生命力

光谱是一个活的文件,不是一次性的文物:

- ** 决定改变时更新** ——如果发现数据模型需要改变,首先更新光谱,然后执行.
- **范围变化时更新** ——增加或切除的特征应在谱中反映.
- **提交光谱** —— spec属于版本控件与代码并列.
- **参考 PR 中的 spec** ——链接到每个公关执行的光谱区段.

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "这很简单,我不需要一个谱" | 简单的任务不需要 *长* 光谱,但是它们仍然需要接受标准。双线光谱是好的。 |
| "我编码后再写" |  这是文档,不是规格,Spec的价值在于强迫清晰度 *在此之前* 代码。 |
| "谱子会拖慢我们" | 15分钟的光谱阻止了几小时的重修. 15分钟后的瀑布在15小时内击败了调试. |
| "无论怎样要求都会改变的" |  因此,这个Spec是活的文件,一个过时的Spec仍然比没有Spec更好. |
| "用户知道自己想要什么" | 即使是明确的请求也有隐含的假设,光谱会显示这些假设. |
| "这是个大特色,拆分是高空的" |  如果接受标准组合成可独立测试的组,那么单一的规格会迫使每个下游任务在整个合同中都有道理。 十线能力图是廉价的替代方案。 |
| "我会在计划期间分解" | 计划切片到一个规格内的任务。 到那时, 超大小的文物已经存在了 。 模块界限和依赖方向必须先决定, 然后再写出规格, 而不是之后 。 |

## Red Flags


- 开始写代码而没有任何书面要求
- 问"我应该开始建设吗?" 在澄清"Done"是什么意思之前
- 执行任何规格或任务清单未提及的特征
- 做出建筑决策而不记录这些决定
- 跳过光谱,因为"很明显要建造什么"
- 写入光谱并在同一回合开始计划或代码
- 其要求涉及若干独立可测试能力的一个类别
- 模块边界或建设秩序在执行期间被暗中决定,因为前方没有核准能力地图

## Verification


 在着手实施之前,确认:

- [ ]  该分类涵盖所有六个核心领域
- [ ] 人类已经审查并批准了光谱
- [ ]  在保存了光谱后,转弯结束;后转弯获得批准
- [ ] 成功标准是具体和可检验的
- [ ] 边界 (Always/Ask First/Never) 定义
- [ ] 光谱被保存到寄存器中的文件
- [ ]  如果请求捆绑了几种可独立测试的能力, 则会绘制一个能力图 (模块 ID、 依赖方向、 构建秩序)  在任何模块规格被写入之前被批准
- [ ]  每个模块光谱追踪到已核准的地图中的模块 ID
