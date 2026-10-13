---
name: code-review-and-quality
description: Conducts multi-axis code review. Use before merging any change. Use when reviewing code written by yourself, another agent, or a human. Use when you need to assess code quality across multiple dimensions before it enters the main branch. Use when asked to review a diff or a pull request, even when the diff is pasted inline.
---

# 守则审查和质量

## Overview


带有质量门的多维码检讨,每次更改在合并前都会被检讨——没有例外,检讨包括五轴:正确性,可读性,架构,安全和性能.

**批准标准:**  当它确实改善了整体的代码健康时, 批准一个修改,即使它不完美。完美代码并不存在,它的目标就是不断的改进。不要阻止一个修改,因为它不是你会怎样写的。如果它改进了代码库,遵循了项目的常规,就批准它。

## When to Use


-  在合并任何 PR 或更改之前
- 完成特性执行后
-  当另一个代理或模型生成代码时,您需要评估
- 重构已有代码时
- 任何错误修正后 (既审查固定测试又审查回归测试)

##  五轴审查

每项审查都评估这些方面的守则:

### 1. 正确性

密码是按它所说的做的吗?

-  它是否符合规格或任务要求?
- 是否处理了边缘案件 (无效, 空的, 边界值)?
- 已处理错误路径 (不只是快乐的路)?
- 测试是否通过所有测试?测试是否真的测试正确?
-  有没有一个差错, 种族条件, 或状态不一致?

### 2. 可读性和简洁性

另一位工程师可以吗? (或代理人)  在作者不解释的情况下理解这个代码吗?

- 名称是否具有描述性质,是否符合项目公约? ( 没有 `temp`, `data`, `result`  无上下文)
- 控制流直截了当吗? (避免巢穴三角形,深层回击)?
- 代码是按照逻辑排列的 (相关编码分组, 清晰的模块边界)?
- 是否有任何"聪明"的伎俩应该简化?
- **能不能少说几句话?** (1000行,只要100镑就是失败了)
- **抽象获得其复杂性吗?** ( 在第三次使用之前不要概括)
- 评论是否有助于澄清非明显的意图? ( 但不要评论明显的代码.)
- 是否有已死亡的密码文物:没有变量 (`_unused`)反向比对shims,或 `// removed` 评论?
- **新的条件性栓在不相关流上了吗?** 这是一种设计气味,而不是一丝不苟——将逻辑推向自己的帮助者,状态,或政策,而不是抚摸现有的路径.
- **重复以相同形状的条件出现吗 ?**  它们信号缺失的模型或调度器,"临时"分支通常是永久债务.

### 3. 结构

改变是否适合系统的设计?

-  它是遵循现有模式还是引入新的模式?如果是新的模式,它是否合理?
-  它是否保持了干净的模块边界?
- 是否应该共享代码重复?
-  是向正确的方向流动的依赖性 ( 没有循环依赖关系)?
- 抽象级别是否合适 ( 没有过度设计,没有太搭配)?
- ** 这个重构是降低复杂性还是仅仅转移它?** 将读者必须坚持的概念数起来才能跟上变化。如果一个“更清洁”的版本没有改变,那它就不是更干净的了 — — 更喜欢使整个分支、模式或层次消失在重新集中的逻辑之上的重组。最好删除一个抽象来擦亮它。
- **特性特定逻辑是否漏入共用或通用模块?** 将逻辑保存在自己的层中,重新使用已有的犬类帮助器而不是近似复制,不要使建筑漂移正常化.
- **类型界限是否明确?** 问题无端 `any`/`unknown`/optional/casts 并且无声回落于一个不明确的无变体上——使边界清晰,往往使周围的控制流更加简单.

### 4. 警卫

详细安保指导见 `security-and-hardening`变革是否带来脆弱性?

- 用户输入是否得到验证和消毒?
- 密码、日志和版本控制是否有秘密?
- 已经是 authentication/authorization 检查哪里需要?
- SQL 查询是否参数化 ( 无字符串连接)?
- 是否对输出进行编码以防止 XSS ?
- 信任来源的依赖性是否没有已知的脆弱性?
- 外部来源的数据 (API、日志、用户内容、配置文件) 被看成是不信任吗?
-  在逻辑或渲染中使用之前,外部数据流是否在系统边界上被验证?

### 5. 业绩

详细概况和优化情况见 `performance-optimization`. 变化是否带来性能问题?

-  有N+1查询模式吗?
- 是否有无限制的循环或不受限制的数据采集?
- 任何同步操作应该同步吗?
- 任何不必要的重交 UI 组件?
- 名单上的终点有缺失的标注吗?
-  在热路径中创建的大型对象吗 ?

## 结构性补救

 当你标出一个结构问题时, 提议移动—— 而不仅仅是问题。 仅说“ 这很复杂” 的回顾让作者猜测。 联系一个命名的重组 :

- **替换条件链** 带有打字型号或明确调度器。
- **折叠重复的分支**  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}进入一个更清晰的流程
- **与业务逻辑分开的管弦乐**  所以,每个人都自己读。
- **移动特定特性逻辑** 出自拥有该概念的软件包中的共享模块。
- **重新使用 Cononic 助手** 而不是几乎重复的说话。
- **使类型边界明确**  所以下游的分支消失了。
- ** 删除过关包装** 增加间接性而不澄清 API.
- **提取一个助手, 或拆分一个大文件** 输入有重点的模块。

更喜欢把移动的碎片 移到同样复杂的地方

## 更改大小

小型、重点突出的改变比较容易审查,比较容易合并,部署起来比较安全。

```
~100 lines changed   → Good. Reviewable in one sitting.
~300 lines changed   → Acceptable if it's a single logical change.
~1000 lines changed  → Too large. Split it.
```

**注意文件大小, 不只是 diff 大小 。**  一个小的diff仍然可以把文件推过健康的边界——大约1000个. * 共计* 单行文件的行 (区别于~1000 *已更改*- 线阈值高于) 是一个常见的检查信号,而不是硬封顶。当更改在物质上长出一个已大的文件时,询问是否要提取帮助器、子组件或模块 *首个*分解,然后添加。

**何谓"一变":**  一个单一的自成一体的修改,涉及一件事,包括相关的测试,并在提交后保持系统功能. 一个特性的一部分——而不是整个特性.

** 在变化太大时分割策略 :**

| 战略 | 怎么样? | 何时 |
|----------|-----|------|
| **堆叠** | 提交一个小的更改, 以它为基础启动下一个更改 | 序列依存关系 |
| ** 按文件组** |  对需要不同审查者的群体进行单独修改 | 贯穿各领域的关切问题 |
| **水平** | 创建共享 code/stubs  首先,然后消费者 | 层状结构 |
| **垂直** | 切入更小的全层片段 | 特色工作 |

** 当大修改可以接受时:** 完成文件删除和自动重构,其中审查者只需要验证意向,而不是每行.

**与特性工作分离重构.** 重构已有代码并添加新行为的更改是两个更改——分别提交。小的清理 (变量重命名) 可由审查员斟酌决定列入。

## 更改描述

 每一次更改都需要一个在版本控制历史中独占的描述.

** 第一行:** short, request, 獨立. "Delete the FizzBuzz RPC" 不是"Delete the FizzBuzz RPC" 信息必须足够丰富,可以让寻找历史的人不读diff就能理解变化.

**车身 :** 包含代码本身中看不到的上下文、决定和推理。 与错误数字、 基准结果或相关设计文件链接。 承认存在缺陷时的处理方式 。

**反标:** "Fix bug","Fix building","添加补丁","从A到B移动代码","第1阶段","添加便利功能".

## 审查进程

### 步骤1:了解背景

 在查看代码之前,要了解意图:

```
- What is this change trying to accomplish?
- What spec or task does it implement?
- What is the expected behavior change?
```

### 步骤2:首先审查试验

测试显示意图和覆盖范围:

```
- Do tests exist for the change?
- Do they test behavior (not implementation details)?
- Are edge cases covered?
- Do tests have descriptive names?
- Would the tests catch a regression if the code changed?
```

 通过实验而不是通过阅读来回答最后一个问题。 倒置一个条件, 更改会添加 (放弃否定, 互换 `&&` (单位:千美元) `||`),运行套件,然后从复制件中恢复文件。一个保持绿色的突变就是发现:将缺失的测试大小写命名。关于整个工程的突变分数,请参见 `constraint-driven-development`.

### 步骤3:审查执行情况

以五轴心行走密码:

```
For each file changed:
1. Correctness: Does this code do what the test says it should?
2. Readability: Can I understand this without help?
3. Architecture: Does this fit the system?
4. Security: Any vulnerabilities?
5. Performance: Any bottlenecks?
```

### 步骤4:对调查结果进行分类

将每条评论标注为其严重性,使作者知道需要什么对可选性:

|  前缀 | 含义 | 作者动作 |
|--------|---------|---------------|
| *( 无前缀)* | 所需变动 | 合并前必须先地址 |
| **关键:** | 块合并 | 安全脆弱性、数据损失、功能中断 |
| **内特:** | 次要, 可选 | 作者可能忽略 — 格式化, 样式偏好 |
| ** 可选 :** / **考虑:** |  建议 | 值得考虑,但不需要 |
| **A. 结 论** | 仅供参考 | 无需采取行动——供今后参考的背景 |

 这就使作者不能将所有反馈视为强制性的,也不能将时间浪费在可选建议上.

**领领一切有情.**  用杠杆来排序发现:首先正确和安全,然后是结构回归和错失简化,然后是其他一切。不要把一个真正的问题埋在化妆品硝酸盐下面——几个高定罪的评论比一个长的列表要好。如果你有一个结构性问题和十个硝酸盐,那么结构问题就是了。 * 为:* 检讨。

### 步骤5:核查核查

 请检查access-date=中的日期值 (帮助) 作者的核实故事:

```
- What tests were run?
- Did the build pass?
- Was the change tested manually?
- Are there screenshots for UI changes?
- Is there a before/after comparison?
```

##  多模式审查模式

 对不同的审查角度使用不同的模型:

```
Model A writes the code
    │
    ▼
Model B reviews for correctness and architecture
    │
    ▼
Model A addresses the feedback
    │
    ▼
Human makes the final call
```

 这抓住了单一模式可能错过的问题——不同的模式有不同的盲点。

**复议代理人示例提示 :**
```
Review this code change for correctness, security, and adherence to
our project conventions. The spec says [X]. The change should [Y].
Flag any issues as Critical, Required, Optional, or Nit.
```

## 死亡代码卫生

 在修改或执行更改后,请检查孤儿代码:

1. 识别现在无法获取或未使用的代码
2. 明确列出来
3. ** 删除前询问 :** "我应该去掉这些现在没有使用的内容: [列表]?"

 不要把死代码留在这儿——它迷惑了未来的读者和特工们,但是不要默默地删除你不确定的东西,在怀疑的时候,请问.

```
DEAD CODE IDENTIFIED:
- formatLegacyDate() in src/utils/date.ts — replaced by formatDate()
- OldTaskCard component in src/components/ — replaced by TaskCard
- LEGACY_API_URL constant in src/config.ts — no remaining references
→ Safe to remove these?
```

##  审查速度

慢评会阻断整个团队. 上下文切换审查的成本低于对他人施加的等待成本.

- **在一个工作日内答复** - 这是最大的,不是目标
- **理想的节奏:**  在审查请求到达后不久即应答,除非深入有重点的编码。 典型的变化应在一天之内完成多个审查回合。
- **将迅速的个别反应列为优先事项** 快速反馈可以减少挫折感,即使需要多轮
- ** 大变化 :** 要求作者拆分 而不是审查一个巨大的变化集

## 处理分歧

 在解决复审纠纷时,适用这一等级:

1. **技术事实和数据** 推翻意见和偏好
2. **样式指南** 是对样式事项的绝对权威
3. **软件设计** 必须根据工程原则评估,而不是个人偏好
4. **代码库一致性**  如果它不降低整体健康,是可以接受的。

**不接受"我待会再清理".** 经验表明,推迟清理很少发生。在提交之前必须进行清理,除非这是真正的紧急情况。如果周围的问题无法在修改中得到解决,则需要提交自指派的bug。

## 诚实的回顾

 在审查代码时——无论是你写的,还是另一个特工,还是一个人类:

- ** 别踩橡皮板**  没有审查证据的"LGTM"对任何人都没有帮助.
- ** 不要软化 真正的问题。** "这可能是一个小问题" 当它是一个错误 会击中生产 是不诚实的。
- **尽可能量化问题。** "这个N+1的查询会在列表中每项添加~50ms"比"这可能会很慢"要好.
- **退却有明显问题的方法.** 协同性是审查中的一个失败模式。如果执行有问题,请直接说,并提出替代办法。
- **顺理成章地接受推翻。**  如果作者有完整的上下文和不同意见,请听从他们的判断,评论代码,而不是人——重新界定个人的批评,以专注于代码本身.

## 依附纪律

守则审查的一部分是依赖性审查:

** 在添加任何依赖性之前:**
1. 现有的堆栈是否解决了这个问题? (经常是这样)
2. 依赖的程度有多大? (检查捆绑的撞击。)
3.  它是否得到积极维护? ( 请检查access-date=中的日期值 (帮助))
4.  它知道弱点吗? (`npm audit`)
5. 驾照是什么? (必须与项目兼容 。)

**规则:** 将标准图书馆和现有公用设施置于新的依附关系之上,每个依附关系都是一种责任。

**提升现有依赖性** 与其它代码一样是一个代码更改,最危险的升级是用"bump deps"这样的信息进行批量合并的代码. 以相同的学科审查它们:

1. **阅读更改日志,而不仅仅是版本编号.** Semver是维护者可能没有遵守的承诺——一个“patch”可以携带行为变化。对于一个大的突起,请读读迁移笔记,找出什么是破碎的。
2. ** 每个变化一个依赖性。** 升级和合并 (或小型相关组别)。当一个大块撞断了建筑时,你失去了哪个包裹完成了它;一个单包的改变使得原因明显,还原干净。
3. ** 让测试决定。** 升级之前由绿色套房验证 *财务报告和财务报告* 之后,而不是通过“它安装了”。如果覆盖范围围绕依赖者的行为是薄的,那么这个缺口就是真正的发现——首先增加一个测试。
4. **注意过渡图。**  大多数已安装的软件包都是无人直接选择的。 请审查锁定文件 diff, 而不仅仅是 `package.json`;一个单一的直接凸起可以拉动数十个间接变化.
5. **保持锁文件诚实。** 把它交给它,审查它的潜水器, 并且永远不要手动编辑它。锁文件是真正钉住什么船的东西。

用于分解 `npm audit` A. 调查结果和供应链风险 (定型、 损坏的维护者),跟着 `security-and-hardening` 技能——本节涵盖升级 *工作流程* 这个是安全判决的

##  审查清单

```markdown
## Review: [PR/Change title]

### Context
- [ ] I understand what this change does and why

### Correctness
- [ ] Change matches spec/task requirements
- [ ] Edge cases handled
- [ ] Error paths handled
- [ ] Tests cover the change adequately

### Readability
- [ ] Names are clear and consistent
- [ ] Logic is straightforward
- [ ] No unnecessary complexity

### Architecture
- [ ] Follows existing patterns
- [ ] No unnecessary coupling or dependencies
- [ ] Appropriate abstraction level
- [ ] Refactors reduce complexity rather than relocate it
- [ ] No feature logic in shared modules; file stays within a healthy size

### Security
- [ ] No secrets in code
- [ ] Input validated at boundaries
- [ ] No injection vulnerabilities
- [ ] Auth checks in place
- [ ] External data sources treated as untrusted

### Performance
- [ ] No N+1 patterns
- [ ] No unbounded operations
- [ ] Pagination on list endpoints

### Verification
- [ ] Tests pass
- [ ] Build succeeds
- [ ] Manual verification done (if applicable)

### Verdict
- [ ] **Approve** — Ready to merge
- [ ] **Request changes** — Issues must be addressed
```
## 另见

- 详细安保审查指南见: `../../references/security-checklist.md`
-  关于业绩审查检查,见 `../../references/performance-checklist.md`

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "它的工作,这是足够的好" | 无法读取、不安全或建筑错误的工作代码 会产生复杂的债务。 |
| "我写了,所以我知道这是对的" | 作者对自己的假设视而不见,每个变化都从另一组人眼中受益. |
| "我们待会再清理" | 以后从不来,审查是质量门——使用它。在合并之前需要清理,而不是在合并之后。 |
| "AI生成的代码可能很好" | AI代码需要更多的审查,而不是更少,它既自信又可信,即使是错误的. |
| "考试通过,所以很好" | 测试是必要的,但还不够。它们不能捕捉到建筑问题、安全问题或可读性问题。 |
| "重塑使它更干净" | 重新定位复杂性并不是减少它,如果读者仍然持有相同数量的概念,结构没有改进——寻找分支消失的版本. |
| "这只是这个文件的一个小补充" |  小的diffs仍然将文件推过健康的大小,将树枝螺栓到无关的流中。判断由此产生的结构,而不是diff的大小。 |
| "这只是一个版本的凸起" |  一个突起是您没有写的行为改变。 读取更改日志; 成像不能保证没有断裂 。 |
| "我会在一个公关里升级所有东西 以节省时间" |  一个大块的凸起打破了建筑隐藏了哪个软件包完成了它。每个变化都有一个依赖性使得原因和回归性保持干净。 |

## Red Flags


- 公关合并,未经任何审查
- 检查,只有在测试通过时才进行检查 (忽略其他轴)
- “LGTM”没有实际审查的证据
-  在不进行以安全为重点的审查的情况下进行对安全敏感的改革
- 大型的公关,"太大了,不能正常审查" (拆开他们)
-  没有用错误修正 PR 进行回归测试
-  没有严重性标签的审查评论——使需要什么对可选性变得不明确.
- 接受"我晚点再修"——它永远不会发生
-  一个在代码周围移动而不减少阅读器必须持有的概念数的重构器
- 正在生成一个已大文件而非拆解文件的更改
- 分散到不相关的代码路径中的新条件 (缺少抽象)
- 与现有空格重复的铃声帮助器, 或放置在共享模块中的特性逻辑
- 批量的"泵依赖性" PR,没有更改日志审查,没有每包隔离
- 锁定文件的更改, 是手动编辑的, 未承诺的, 或合并而不审查它的 diff 。

## Verification


 审查完成后:

- [ ]  所有关键问题都得到解决
- [ ] 所需所有资源 ( 无前缀) 作出改变或明确推迟,并说明理由
- [ ] 测试通过
- [ ] 构建成功
- [ ] 校对故事被记录下来 (改变了什么,如何核实它)
- [ ] 依赖性升级被对照其更改日志审查,每个包被隔离,并由一个绿色套房进行验证,并审查锁文件diff

**假定阻断器 :** 表面并针对其中每一个提出更简单的设计; 只有当改变积极使结构更糟糕时, 才会升级为必需: 重置复杂度而不是减小复杂度的重构; 将文件推过大小边界而无分解的改变; 共享模块中添加的特性逻辑; 现有条形帮助器的近似复制; 隐藏一个不明确的不变量的无声倒置。
