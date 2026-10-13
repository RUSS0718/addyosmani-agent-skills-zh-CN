---
name: interview-me
description: Extracts what the user actually wants instead of what they think they should want. Achieves this through one-question-at-a-time interview until ~95% confidence about the underlying intent. Use when an ask is underspecified ("build me X" without "for whom" or "why now"), when the user explicitly invokes ("interview me", "grill me", "are we sure?", "stress-test my thinking"), or when you catch yourself silently filling in ambiguous requirements before any plan, spec, or code exists.
---

# 采访我

## Overview


 人们要求的和他们真正想要的都是不同的东西。他们要求“仪表盘”是因为人们要求的,而不是因为一个仪表盘解决了他们的问题。他们说“让它更快”而没有数字可以击中。

找到这个缺口的最廉价时刻是在任何计划、规格或代码存在之前。 一旦你开始建造,转换成本是真实的,用户会把错误的东西合理化为“足够好”的东西。 错误的配置被锁定了。

 这种技能在付出任何代价之前就缩小了差距。另一种“定义阶段”技能假设你已经大致知道自己想要什么: `idea-refine`  从一个想法中产生变化, `spec-driven-development` 写下要求, `doubt-driven-development` (笑声) 压力测试了一个在您起草计划之后的计划。“面试我”是摆在所有人面前的部分,您一次问一个问题,并附上你最好的猜测,直到您能够预测用户在说之前会说什么。

## When to Use


在下列情况下应用此技能:

- 询问中至少少了一个: ** 谁** 用户是, ** 为什么**  他们想要它,什么 **成绩** 看起来像,什么束缚 **约束**  为:
- 请求是常规的,而不是具体的 ("建造我X","让它更快")  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你却不能不猜想地整理会议 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}而你却无法解开会议
-  你是想先假设你还没有浮出水面
- 用户没有说当两个合理者处于紧张状态时,他们最优化的值是多少 (简便与灵活性、成本与速度)
- 用户明确引用:"访问我","烦我","在我们开始之前,我们确定吗?","压力测试我的思维".

** 不使用时 :**

- 要求是毫不含糊和自足的 (“重命名此变量” ,“ 修正此类型”)
- 用户已明确要求快速进行校验
- 纯信息请求 ("X怎么工作的?","这个代码是做什么的?")
- 机械作业 (重命名、格式、文件移动)
-  您已有95%的置信度; 在假设您没有时重新读取下面的停止状态 。

## 装入限制

 这种技能需要活泼,反应迅速的用户. ** 在非互动上下文中不要引用** 喜欢 CI 输油管,预定运行, `/loop`,或自定义跳动。如果您在其中的其中之一,且请求不够明确,请标注该用户的阻塞符,而不是猜测。

## 进程

###  第1步:假设大小,并有置信号

 在询问任何信息之前, 请记录您当前对用户想要的内容的最好读取 **一句话**,加上一个诚实的自信号码 (0–100%):

```
HYPOTHESIS: You want a way to answer "how are we doing?" in standup, and "dashboard" was the convention that came to mind.
CONFIDENCE: ~30% — missing: who it's for, what "metrics" means in context, and what success looks like
```

数字强迫诚实。 如果您写下一个高数字, 但实际上无法预测用户对接下来三个问题的反应, 数字是错的。 开始于您可以捍卫的置信级别 。

 当置信度低于~70%时,在同一个线上附加一个简短的原因——什么仍未解决或缺失,这完全告诉了用户采访需要浮出水面,防止数字成为模糊的信号.

###  第2步:一次问一个问题,每个问题都附有一个猜测

格式 :

```
Q: <one focused question>
GUESS: <your hypothesis for the answer, with the reasoning that produced it>
```

等待用户在询问下一个问题之前做出反应.

** 为什么一次一个,不是批次:**

- 用户无法对你的假说作出反应, 如果你把它们埋在列表中
- Batches鼓励滑行阅读和表面回答
- 第三个问题往往取决于第一个问题的答案;一次问他们全部都锁在错误的框中
- 用户仔细思考的能量是有限的;一次用一个问题

**为何附上一个猜测:**

- 用户对错误猜测的反应比他们从头到尾生成答案的反应要快
-  它使你接受一个假设 你可能明显错了, 这让你诚实。
-  它会浮出水面 * 您的电话* 假设,即面谈的目的是要揭露

 这里的风险是一个礼貌的用户同意你的猜想是可以接受的。通过明显地愿意做错来缓解风险,偶尔也会以你预期的用户会往后推的方向猜测。

###  第3步:听好,"想要的应该想要的"

 最危险的答案是 用户说一个多么周到的答案 *听起来像* 而不是他们真正想要的。

- 解答模式 - 匹配 - 最佳做法 ("我要它可以伸缩" "干净的建筑")  无细节
- 服从于公约的答复 ("大多数应用软件的方法","标准方法")
-  比如"我应该...","我想我应该...", "良好的工程实践说..."
- Buzzwords作为目标——当"现代","可伸缩","robust"是答案而不是一个特定的结果时.

 当你听到这些, 问的问题是:

> *"如果你不必向任何人说明这个理由,你到底想要什么?"*

 这个问题往往比前五个问题更有效。

###  第4步:在用户自己的言语中重述意图

 当您信心大的时候, 请写回您认为用户想要的 。 请紧紧保持 (5至8行线路),在可能时使用其语言,并构建其结构,以便用户可以逐行确认或校正:

```
Here's what I now think you want:

- Outcome:      <one line>
- User:         <one line — who benefits>
- Why now:      <one line — what changed>
- Success:      <one line — how we know it worked>
- Constraint:   <one line — the binding limit>
- Out of scope: <one line — what we're explicitly not doing>

Yes / no / refine?
```

包括"范围之外"是不可谈判的,一半的错位是对什么是沉默的分歧. * 没有* 正在建设中。

###  第5步:确认——明确是的,而不是"无论你怎么想"

大门是明确的"是的"。 下面是 ** 没有**  对:

- – 用户正在授权, 这意味着他们也没有95%的自信。 重新授权两个具体选项作为选择 。
- "听起来不错" – 模糊不清,问:"你所修炼的是什么?"沉默不是肯定的.
- "当然,我们走吧" – 常常是礼貌的退出,而不是背书。
- 沉默之后是"好的,我们开始吧" – 用户已经放弃了采访,没有聚合,停止并询问你是否错过了什么.

 如果他们纠正了你, 将更正折叠并重写。 循环直到你得到明确的是 。

### 步骤 6: 终端转动和停止规则 (晶体)

 当用户以明确的"是"确认意向声明时:

1. **送达已确认的意向声明。**
2. **提供下游路径** ( {\fn黑体\fs20\shad2\2aH82\3aH20\4aH33\fscx95\3cH592001\be1}我愿意把它保存下来 {\fn黑体\fs20\shad2\2aH82\3aH20\4aH33\fscx95\3cH592001\be1}我愿意把它保存下来 `docs/intent/[topic].md`中,起草一个与 `spec-driven-development`,或移动到 spec 后的任务规划).
3. **停止你的转身 及时。**  不要在此回合引用工具或开始下游工作。 手控返回用户选择下一步 。

### 95%的信心停止

 当你能回答"是"的时候,你就完了:

> * 我能预测用户对接下来三个问题的反应吗?*

 如果有,你也有共同的理解。停止采访并制作重述。如果有,你没有完成,请问下一个问题。

 这是一个可核查的测试,而不是一种氛围。它也有一层:如果你已经走了几轮,仍然无法预测,这就是询问的信息,而不是继续磨磨的理由。停下来告诉用户:“我问了X个问题,我仍然无法预测你的反应。缺少一些基础的东西。想退一步吗?”

## 产出

 这种技能的输出是 ** 经确认的意向声明**:步骤4的重述,第5步有明确的肯定。这是可以交付的。样本、计划和任务清单是下游的;它们消耗了这种技能所产生的意图。

 如果用户希望意图持续下去 ( * 多会项目,交给另一个合作者),提议将其保存到 `docs/intent/[topic].md`。除非他们确认。

## 示例

短时前后.

** 无 `interview-me`:**

```
User:  build me a dashboard for our metrics
Agent: <starts proposing chart libraries and layouts>
```

代理商已经假设:它是为了谁,衡量标准是什么,"对于我们的团队"意味着什么,成功是什么样子,这些都没有被表述出来,每个假设都是一个使用者以后可以说"那不是我的意思"的地方.

**与 `interview-me`:**

```
User: build me a dashboard for our metrics

Agent:
HYPOTHESIS: You want a way to answer "how are we doing?" in standup, and "dashboard" came to mind because that's the conventional answer.
CONFIDENCE: ~30% — missing: who it's for, what "metrics" are, and what success looks like

Q:     When you say "how are we doing?", who's asking — you alone, the engineering team in standup, or up the chain (e.g., your manager, an exec, a board)?
GUESS: engineering team in standup, because "we" usually scopes that way and standups are where this question gets asked. If it's for execs the metrics and the framing change a lot.

User: It's actually for me. I keep losing track of which experiments are running and what their early signal looks like.

Agent:
Updated read: not a team dashboard at all. It's a personal experiment tracker. Confidence: ~60% — still missing: what "early signal" means and what done looks like.

Q:     Is the gap that you don't know which experiments exist, or that you can't see their results in one place?
GUESS: the second one. You have a list of experiments somewhere, but their results live in five different tools, and reconciling them by hand is what you're tired of.

User: First one actually. I literally don't have a list. They're spread across different docs.

Agent: <continues>
```

 两个问题,特工发现实际的询问不是"仪表盘",而是"列表",不同的文物,不同的范围,不同的工作,仪表盘会是错误的.

## 与其他技能的互动

- **`idea-refine`**:下游。如果确认的用意是“我想要X,但我不知道如何去探索它。” `idea-refine` 与现在明确的意图产生差异。
- **`spec-driven-development`**: 下游。 如果确认的意图是具体的 ("我要X为Y用户提供Z成功标准".),将手拿开 `spec-driven-development` 写下来。
- **`planning-and-task-breakdown`**: 下游两跳这个技能 (光谱后).
- **`doubt-driven-development`**: 时间线的相反端。 Interview- me 是决定前的意向提取; 怀疑驱动是决定后文物审查。 两者的捕获差异, 但在不同的时刻。
- **`source-driven-development`**:正交. Interview-me澄清用户想要什么; SDD 核实框架事实,它们不竞争

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "问的够清楚了" |  如果你现在不能将用户的预期结果写成一句话,那么询问就不清楚了,在决定前先运行步骤1. |
| "问太多问题了,浪费他们的时间" | 时间浪费在4-6个有针对性的问题上是很小的。 建造错误的东西浪费的时间是巨大的,而使用者是承担这一代价的人。 |
| "我会想出办法的" | 代码存在后的切换成本是现在的 10x 。 执行过程中的发现是重做 。 |
| "他们说,"不管你怎么想," 所以我应该只是决定" | "无论你怎么想"是授权,而不是决定,重新提出两个具体的选项作为选择. |
| "我应该给他们一些选择 从" | 选项在用户知道自己想要什么时起作用, 并且正在权衡权衡。 他们还不知道自己想要什么 。 列出选项会扩大搜索范围; 询问会缩小搜索范围 。 |
| "如果我附上我的猜想,我领导他们" | 铅是关键所在。 反应比从零开始产生要快。 风险是杂乱无章的,而不是领先的; 通过明显愿意做错而减轻。 |
| "我们谈够了,我明白了" | 测试一下:你能预测他们对接下来三个问题的反应吗?如果不是,你还不明白. |
| "用户答应了,我们完了" |  如果 " 是 " 后面是含糊的重复,或者 " 听起来不错 " , " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是) 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是 " 是) 是 " 是 " 是) 是) 是 " 是) 是 " 是) 是 " ) 是 " 是 " ) 是 " ) 是) 是) 是) 是) ) 是) 是) ) 是) 是) ) ” ) ” ) ) ) ) ) ) ) ) ” ) ) ) ) |

## Red Flags


- 单条留言中有三个或三个以上的问题:那是批量的,不是采访
-  一个没有你假设的问题 所附的: 这是调查,而不是实施
- 接受"你认为最好的"作为终极答案
-  在用户明确确认您的重覆之前, 生成一个规格、 计划或任务列表
- 利用工具,或在意图确认后立即开始下游工作,而不是停止工作
- 问题被描述为“什么是最佳做法?” 而不是“你到底想要什么?”
- 用户给出了一个精密的信号解答 ("可伸缩","干净","现代")  你接受它而不调查 这是他们真正想要的
-  三轮或三轮以上 没有你明显的信心上升: 你问错了问题,退后,重塑框架
- 低于~70%的置信号,无故附加:用户若不知道缺少什么,则无法帮助关闭空隙.
-  在用户确认前保存意图文件 (医生本身意味着是 用户没有给出)
-  在重覆中跳过“ 超出范围” 一行 ( 对非目标的沉默分歧是错位的一半)

## Verification


申请后 interview-me:

- [ ] 第一次发言中明确提出一个有信任号的假设
- [ ] 低于70%的每个置信号都带有一行理由 (还没有解决的或者失踪的)
- [ ] 一次问一个问题,每个问题都有代理人的猜测
- [ ]  至少一个“如果你不必证明你需要什么?” 当用户给出一个精密的信号或会议信号的答案时,探测器会运行。
- [ ] 混凝土重现 ( 结果 / 用户 / 为什么现在 / 成功 / 约束 / 超出范围) 被写回给用户
- [ ] 用户以明确 " 是 " 确认了重覆 ( 不是"不管你怎么想" 不是"听起来不错" 不是"沉默")
- [ ]  在停止点,毒剂可以预测 对接下来三个问题的反应 它会问
- [ ] 接到明确确认后,立即停止转弯,不使用工具或开始下游工作
- [ ] 任何向下游技能的交接 (`idea-refine`, `spec-driven-development`) 被设定在确认意图的范围内,而不是在原始未具体说明的要求的范围内。
