# 管弦乐模式

 此 repo 所赞同的代理管弦图案的参考目录, 外加防标符。 在添加新的斜线命令来协调多个人之前, 或在引入新的“ 包装” 现有人之前, 读取此内容 。

指导性规则: **用户 (或斜线命令)  是管弦乐的,人不会引用他人的。** 技能是个人工作流程中必修的跳跃.

---

## 认可模式

### 1. 直接援引 ( 无管弦乐)

单人 单视角 单件文物 默认和最便宜的选择

```
user → code-reviewer → report → user
```

**使用时 :** 这部作品是一个关于一件艺术品的一个视角, 你可以用一句话来描述它。

**实例:**
- "审查这份公关" —— `code-reviewer`
- 寻找安全隐患 `auth.ts`" → `security-auditor`
- 检查流量少了什么测试? `test-engineer`

**费用:**  你总是应该把布局模式比对基准线

---

### 2. 单人斜线命令

 用项目的技能包裹一个人a 的斜线命令。 每次将用户从对工作流程的重新解释中拯救出来 。

```
/review → code-reviewer (with code-review-and-quality skill) → report
```

**使用时 :** 同样的单人引用 重复发生 相同的设置。

** 此回购中的例子 :** `/review`, `/test`, `/code-simplify`.

**费用:** 与直接引用相同。斜线命令只是保存的提示 。

**反信号:**  如果斜线命令的身体大多是"决定要呼叫哪个人",则删除它,让用户直接呼叫该人.

---

### 3. 与合并并行的粉丝出炉

多人同时在同一输入上操作,每个输入都生成独立报告。合并步骤 ( 在主要代理人的背景中) 把它们合成一个单一的决定。

```
                    ┌─→ code-reviewer    ─┐
/ship → fan out  ───┼─→ security-auditor ─┤→ merge → go/no-go + rollback
                    └─→ test-engineer    ─┘
```

**使用时 :**
- 子任务是真正独立的 ( 没有共享的可变状态, 没有命令依赖)
-  每个子代理从自己的上下文窗口中受益
- 合并步骤小到可以停留在主上下文中
- 长时超时空问题

** 此回购中的例子 :** `/ship`.

**费用:** N 并行子代理上下文 + 1 合并转弯。 高于直接引用, 但更快的时钟并生成更好的报告, 因为每个子代理都只关注其单一视角 。

**采用这一模式前的验证清单:**
- [ ]  我可以同时运行所有副代理 而不命令问题吗?
- [ ] 每个人是否都产生了不同的 *类型* 找到,而不是从不同角度 找到相同的发现?
- [ ] 合并步骤是否适合主代理的剩余上下文 ?
- [ ] 用户等待的时间够长吗?

 如果有任何回答是"不",就回到直接引用或单人命令.

---

### 4. 顺序管道作为用户驱动的斜线命令

用户按定义的顺序运行斜线命令, 包含上下文 (或承诺历史)  没有管弦乐团的代理——使用者就是管弦乐团。

```
user runs:  /spec  →  /plan  →  /build  →  /test  →  /review  →  /ship
```

**使用时 :** 工作流程有依赖关系 ( 每个步骤都需要上一步骤的输出) 和人类在步骤之间的判断会增加价值。

** 此回购中的例子 :** (a) 整个防御工事—— 计划—— 基本计划—— 核查—— 审查—— 船舶生命周期。

**费用:**  每个步骤一个子代理上下文。因为没有管弦乐器代理,所以对管弦乐层是免费的。

** 为什么不把它自动化:**  一个LLM"生命周期管弦乐手"将 (a)  在步骤之间失去细微的区别 因为它必须概括为交接, (b) 跳过早期抓错方向的人行检查站, (c) 将代币成本翻一番,通过抛写转折。

---

### 5. 研究隔离 (上下文保护)

 当一项任务需要读取大量不应污染主上下文的材料时,产出一个只返回文摘的研究副剂.

```
main agent → research sub-agent (reads 50 files) → digest → main agent continues
```

**使用时 :**
-  主要会议需要继续侧重于下游任务
- 调查的结果比它消耗的输入要小得多
- 决策质量从主要代理人那里得到好处,主要代理人有思考的余地。

**实例:** "寻找这个堕落的每一个呼叫站点" API 说出这30个ADR对拐弯抹角的说法

**费用:**  一个孤立的子代理上下文。任何时间都值得,替代方案都是将数百份文件加载到主上下文中。

**打开 Claude Code,使用内置 `Explore` 子剂** 而不是定义一个习惯研究的人a。 `Explore`  在海库上运行,被拒绝 write/edit 工具,并且是用于此模式的目的构建的。定义自定义研究子代理 `Explore` 不合时宜 ( 比如,你需要一个特定域的系统 提示模型不会推断).

---

## Claude Code 兼容性

 这个目录是不可知论的 但大多数读者都会把它打开 Claude Code。下面是每个图案的图示 Claude Code原始人——以及平台为我们执行我们规则的地方.

### 一个人住的地方

插件子剂进入 `agents/`  在插件根上。此 Repo 是一个插件 (`.claude-plugin/plugin.json`),这样 `agents/code-reviewer.md`, `agents/security-auditor.md`,以及 `agents/test-engineer.md`  当插件启用时自动发现。不需要路径配置。

### 副代理人对代理团队

Claude Code  有两种平行主义原始。 (与合并平行扇形出局) 地图到 **子剂**。如果需要队友相互交谈,请使用 **代理团队** 换了它。

| | 副剂 | 代理团队 |
|--|-----------|-------------|
| 协调 |  主代理粉丝出局,副代理只报到 | 队友互换消息, 共享任务列表 |
|  一. 背景情况 |  每个子剂拥有上下文窗口 |  每个队友拥有上下文窗口 |
| 何时使用 | 编写报告的独立任务 | 需要讨论的合作工作 |
| 状态 | 稳定 | 实验——需要 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` |
| 费用 | 下调 | 高点,每个队友都是单独的 Claude 实例 |

** 这种repo中的人在两种模式中都有作用.** 产下潜剂时 ( 例如, `/ship`) 他们向主课报告结果,当他们成为队友时 (`Spawn a teammate using the security-auditor agent type…`),它们可以直接挑战对方的发现。人称定义是一样的;只有产卵环境才会改变。

 一个微妙之处: `skills` 财务报告和财务报告 `mcpServers` 人体内的前题字段在作为子代理运行时是荣耀的,但是 ** 当它作为队友运行时被忽略** ——队友负载技能及 MCP  来自您的工程和用户设置的服务器,与普通会话相同。如果一个人a依赖于特定的技能,或者 MCP 服务器正在装入中, 在会话级别配置它, 这样它就可以在两种模式中可用 。

### 平台强制规则

 这个目录中的两条规则不仅仅是会议... Claude Code 强制执行:

- **亚试剂不能产下其他亚试剂** ( 文件的逐字记录)反模式B (呼叫 - 人称) 和反模式D (深人间树) 无法存在于 Claude Code  通过建筑。
- **"没有筑巢队"** ——队友不能自生自灭的队伍,同样的反帕特尔在队伍一级被阻断.

 这表示你可以采用这个目录中的图案,而不担心贡献者意外建造了反标板,它们只会没有加载.

### 内置的子代理来了解

 在定义自定义子代理之前, 请检查其中之一是否涵盖角色 :

| 内建 | 目的 |
|----------|---------|
| `Explore` |  只读代码库搜索与分析。 模式 5 使用此选项 (研究隔离). |
| `Plan` |  在计划模式下只读研究. |
| `general-purpose` |  多步骤任务既需要探索,也需要修改. |

 不要重新定义这些 将你的专家人格分层 (密码审查员、安全审计员、测试工程师)  在他们上面。

### 插件代理的前题限制

插件子代理处理 ** 没有** 支助 `hooks`, `mcpServers`,或 `permissionMode`  前题字段 — 这些字段被默默忽略。 如果未来的人物需要其中之一, 用户必须将文件复制到 `.claude/agents/` 或 `~/.claude/agents/` 换了它。

DO在插件代理中工作的字段为: `name`, `description`, `tools`, `disallowedTools`, `model`, `maxTurns`, `skills`, `memory`, `background`, `effort`, `isolation`, `color`, `initialPrompt`组合键。 `model` 每人,如果你想优化成本 ( 例如,海库 `test-engineer` 覆盖扫描, Sonnet 用于 `code-reviewer`切换为 `security-auditor`).

### 平行地产生多个子剂

内 Claude Code,平行扇形 ( 图案 3) 需要发布 **多个代理工具在一个助手回合中调用**. . . . . . `/ship` 任何新的管弦乐指挥机构都一样

---

## 工作实例: 竞相- 假说调试的代理团队

 此示例显示何时到达 **代理团队** 改为 `/ship`'副剂的扇形外观,两种图案从远处看起来相似——两者都产下同一个三人——但值来自另一个地方.

### 设想方案

> *结业前偶尔会挂断~30秒。 大约每50个会话都会出现一次。 日志中没有错误。 上星期发布后开始 。*

可能的根源 (互相排斥,都符合症状):

1. 新的付款确认流中的种族条件
2. 偶尔会通过一个缓慢同步的网络呼叫的认证检查
3. 查询中缺少一个带有拖车大小的索引
4. 闪闪发光的第三方 API 地点 SDK  在计时截止前悄悄地重试

 一个特工会选择第一个可信的理论 并停止调查。 `/ship`- 典型的潜质粉丝会让每个人独立报告——但是他们的报告从不相遇,所以没有任何东西能排除错误的理论.

这正是特工小组文件所描述的情况: *"随着多个独立调查人员积极试图反驳对方,生存下来的理论更有可能成为实际的根源".*

###  为什么这样? * 没有* a `/ship` 任务

| | `/ship` (子剂) | 代理团队 |
|--|--------------------|-------------|
| 副代理人看见了 | 相同的光圈,不同的镜片 | 共享任务列表, 彼此的信息 |
| 产出 | 3份独立报告 – 1份合并 | 横向辩论-协商一致的根源 |
| 右时 |  你想对已知的文物作出判决 |  你想怎样? *查找( E)* 假说中的文物 |

`/ship`  是判决 Teams探员是调查

### 设置 (一次性,每个环境)

特工团队是实验性的 `~/.claude/settings.json`:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

需求 Claude Code v2.1.32 或以后. 此 repo 中的人物自动被拾取——没有团队配置文件给作者手写.

### 触发提示

以自然语言键入主会话:

```
Users report checkout hangs for ~30 seconds intermittently after last
week's release. No errors in logs.

Create an agent team to debug this with competing hypotheses. Spawn
three teammates using the existing agent types:

  - code-reviewer  — investigate race conditions and blocking calls
                     in the checkout code path
  - security-auditor — investigate auth checks, session handling,
                       and any synchronous network calls added recently
  - test-engineer  — propose tests that would distinguish between the
                     hypotheses and check coverage gaps in checkout

Have them message each other directly to challenge each other's
theories. Update findings as consensus emerges. Only converge when
two teammates agree they can disprove the others'.
```

铅会孕育三个队友 参照现有的人名。 ** 附 录** 给每个队友的系统作为补充指示提示 ( 在团队协调指令之上 领先者安装);上面的触发器成为他们的任务。

### 发生什么事了

1.  每个队友在自己的上下文窗口中运行,从自己的镜头中探索代码库.
2. 队友使用 `message` 直接把发现传送给对方 线索不需要转发
3. 共享任务列表显示谁在调查什么——随时可见 `Ctrl+T` (进程模式) 或用 tmux 窗格 (拆分模式).
4. 何时 `code-reviewer` 找到一个 `Promise.all`  这应该是顺序的,它的信息 `security-auditor` 来确认授权电话不是比赛的一部分 `security-auditor` 核实和答复——要么确认种族是真正的问题,要么产生反证。
5. `test-engineer` 提议对胜出的任何理论进行重点整合测试,小组在宣布共识之前使用该测试加以核实。
6. 铅合成了凝聚的发现并呈送给你.

 你可以通过骑自行车打断任何队友 `Shift+Down` 和打字——用于重定向一个走错路的调查员.

### 什么时候清理

 当调查以根源为根据时,告诉线索:

```
Clean up the team
```

总是通过领先,不是队友来清理 ( 按文件排列: 队友缺乏整齐的团队背景来清理).

### 费用预期

三个索内的队友在10到15分钟的调查时间里 所付出的代价 远远高于三个作为潜质产下的 `/ship`理由如下: * 结论的质量* ——对于错误修复费用昂贵的生产调试,额外的代币是一种讨价还价。 对于例行公关审查,请坚持 `/ship`.

###  这种情况下的反模式

执行( D) ** 没有** 把它重建成一个 `/debug` 斜线命令让粉丝发出副剂. 副剂不能互相传递信息——你会失去使模式生效的对抗辩论. 如果一个工作流程不断出现,将上面的触发器作为片段记录下来,而不是用滥用副剂的斜线命令包裹起来.

### 何时 * 没有*  要使用代理团队

-  对已知的`使用 ' 的有生产限制的裁决 `/ship` (子剂).
-  关于一件文物的一个专家观点 —— 直接引人注意。
- 连续生命周期 (Spec 计划 建设) + 用户驱动的斜线命令 ( 图案 4).
- 带有小文摘的读重研究 • 内置 `Explore` 副剂。

 只有队友才能联系到特工队 **需求** 互相挑战 给出正确的答案

---

## 反毒剂

### A. 路由器人员a ("电磁脉冲器")

一个人,他的工作是决定 哪些人可以打电话。

```
/work → router-persona → "this needs a review" → code-reviewer → router (paraphrases) → user
```

** 为什么它失败了:**
-  无域值的纯路由层
- 增加两个解释性跳动 + 信息损失 + 大约 2x 符号成本
- 用户已经知道他们想要审查;他们可以打电话 `/review` 直接
- 复制斜线命令和意图映射的工作 `AGENTS.md` 已经做了

**取而代之的是怎样做:** 添加或改进斜线命令。文档意图 ~ 命令映射 in `AGENTS.md`.

---

### B. 给另一个人打电话的人a

A `code-reviewer` 内部援引 `security-auditor`  当它看到认证码时。

** 为什么它失败了:**
- 人被设计成一个单一的视角; 将他们连锁起来打败那些
- 简言之, 调用人格的通过会失去 调用人格的上下文
- 失败模式乘法 (哪些人物的输出格式获胜? 哪些规则适用 ?)
- 隐藏用户的成本

**取而代之的是怎样做:** 有个叫人的人a * 建议* 报告的后续审计。用户或斜线命令运行第二个通过。

---

### C. 语句的序号指挥员

 一个打过电话的特工 `/spec`,则 `/plan`,则 `/build`等代表用户使用.

** 为什么它失败了:**
- 失去那些抓错方向的检查站
-  每次交接都总结背景——在漫长的管道中累积漂移
- 双倍代币成本:每一步的管弦乐手转动+副代理转动
- 将用户代理机构从判断最重要的地点删除

**取而代之的是怎样做:** 将用户保留为管弦乐手。文档中推荐的顺序 。 `README.md` 并让用户引用它。

---

### D. 深人种树

`/ship` 调用 a `pre-ship-coordinator`  这叫... `quality-coordinator` 呼唤 `code-reviewer`.

** 为什么它失败了:**
-  每层都添加没有决定值的静态和符号
- 调试成为多层次的调查
- 叶子的人会失去上下文 多个概括步骤

**取而代之的是怎样做:** 将管弦深度保持在最多 1 处 (斜线命令 = 人)。合并发生在主要代理人。

---

## 决策流程

 在考虑一个新的精心安排的工作流程时,走这条流程:

```
Is the work one perspective on one artifact?
├── Yes → Direct invocation. Stop.
└── No  → Will the same composition repeat?
         ├── No  → Direct invocation, ad hoc. Stop.
         └── Yes → Are sub-tasks independent?
                  ├── No  → Sequential slash commands run by user (Pattern 4).
                  └── Yes → Parallel fan-out with merge (Pattern 3).
                           Validate against the checklist above.
                           If any check fails → fall back to single-persona command (Pattern 2).
```

---

## 何时在此目录中添加新模式

仅在 :

1.  在真正的工作里你至少用过两次这个模式
2.  您可以在这张遗书中 列出一个能证明它的混凝土文物
3.  你可以解释为什么 现有的模式是行不通的
4.  你可以描述它反模式的影子 ( 而人们却错误地建造它。)

 不成熟的目录条目成为无人遵循的渴望文件.
