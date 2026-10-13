---
name: doubt-driven-development
description: Subjects every non-trivial decision to a fresh-context adversarial review before it stands. Use when you want every assumption cross-examined before proceeding, when stress-testing a plan for hidden failure modes, when correctness matters more than speed, when working in unfamiliar code, when stakes are high (production auth, security-sensitive logic, a high-stakes migration, irreversible operations), or any time a confident output would be cheaper to verify now than to debug later.
---

# 令人怀疑的发展

## Overview


自信的答案并不正确。 长时间的会话积累了将假设悄悄变成“事实”而不引起任何人的注意。 由怀疑驱动的发展就是实现新内容审查员的纪律 — — 偏向于 **反驳**,不批准——在任何非三角产出出现之前。

 这不是 `/review`. `/review`  这是在飞行中的一种姿态:对非部落性的决定进行交叉审查,而校正过程却仍然很便宜。

## When to Use


 一个决定是: ** 非三边形**  当其中至少一项属实时:

-  它引入或修改分支逻辑
-  它跨越一个模块或服务边界
-  它声称,类型系统或编译器无法核实的财产 (线条安全、同位素、订单、变化剂)
- 其正确性取决于未来读者所看不到的上下文
-  它的爆炸半径是不可逆的 (生产部署、数据迁移、公共 API 变动)

应用技能时 :

- 即将在不确定的情况下做出建筑决定
- 即将执行非部落代码
- 即将宣称一个非明显的事实 ("这是安全的","这个秤","这个匹配的规格")
-  在代码中工作 你并不完全了解

** 不使用时 :**

- 机械作业 (重命名、格式化、文件移动)
- 遵循明确、毫不含糊的用户指示
- 读取或总结现有代码
- 带有明显正确性的单行变化
- 纯工具操作 (运行测试, 列出文件)
- 用户已明确要求快速进行校验

 如果你怀疑每一次键盘, 则一无所有。 技能仅适用于上文定义的非三角决定 。

## 装入限制

 这个技能是专为 ** 主会管弦乐**中,步骤3 (DOUBT,详述如下:)  可以培养出新内容审查员。

- ** 不要将这种技能添加到人身上 `skills:`  前题。**  第3步之后的人会产生另一个人—— 由第3步所明确禁止的管弦乐反派式。 `../../references/orchestration-patterns.md` ("人不能引用别人").
- ** 如果您发现自己从子代理上下文中应用了这种技能** (地点 Claude Code 防止嵌入子剂产卵): 首选的路径是向用户表面显示怀疑驱动不能运行巢穴并让主会话处理它。作为最后手段,退化的自我质疑倒计时存在 — 将ARTIFACT + CONTRACT 重写为新的自我推动器,从您之前的推理中用一个坚硬的精神分隔符,并走步骤1 - 5。 ** 不审查新内容** ( 你带着自己的背景与你),因此将结果标注为退化,并倾向于在用户可以到达时升级。

## 进程

应用技能时复制此核对表 :

```
Doubt cycle:
- [ ] Step 1: CLAIM — wrote the claim + why-it-matters
- [ ] Step 2: EXTRACT — isolated artifact + contract, stripped reasoning
- [ ] Step 3: DOUBT — invoked fresh-context reviewer with adversarial prompt
- [ ] Step 4: RECONCILE — classified every finding against the artifact text
- [ ] Step 5: STOP — met stop condition (trivial findings, 3 cycles, or user override)
```

###  第1步:索赔----表层

以两行或三行命名决定 :

```
CLAIM: "The new caching layer is thread-safe under the
        read-heavy workload described in the spec."
WHY THIS MATTERS: a race here corrupts user data and is
                  hard to detect in QA.
```

 如果你不能用紧凑的方式写出这个主张, 你有一种感觉,而不是一个决定。在仔细检查之前,先看清楚它。

### 步骤2:EXTRACT——最小的可审查单位

 新内容审查员需要: **文物**  页:1 **合同**而不是旅程。

- 代码:diff或函数——不是整个文件
-  决定:以3至5句表示的提案,加上它必须满足的限制因素
- A. 索 赔:索赔加上据称支持索赔的证据 (与步骤1索赔区块区别开来,该区块是正在审查的指挥员的假设)

脱掉你的推理,如果你交出结论,你就会重新得到你的结论的验证,这个单位必须足够小,一个评论员可以把它放在一个读数中——如果是一个500行的PR,先分解.

### 步骤3:DOUBT——请新内容审查员发言

审阅员的催促 **必须是对抗性的**弗雷明决定答案。

```
Adversarial review. Find what is wrong with this artifact.
Assume the author is overconfident. Look for:
- Unstated assumptions
- Edge cases not handled
- Hidden coupling or shared state
- Ways the contract could be violated
- Existing conventions this might break
- Failure modes under unexpected input

Do NOT validate. Do NOT summarize. Find issues, or state
explicitly that you cannot find any after thorough examination.

ARTIFACT: <paste artifact>
CONTRACT: <paste contract>
```

**仅通过ARTIFACT + Contract。不要通过索赔。** 将您的结论交给审查者会偏向于协议,审查者必须独立地确定该文物是否符合合同。

内 Claude Code中,基于角色的审查员 `agents/`  从设计上孤立的上下文开始,在这里可以使用——见 `agents/` 用于名册和每个域的匹配。

**上面的对抗性提示优先于人物的默认反应形状.** 喜欢这样的人 `code-reviewer` 写来产生平衡的判断,既要强又要弱; 由疑问驱动的需要问题只输出。 将对立的迅速字片输入引用中,这样它就能推翻人物的默认。 如果一个人物的反应形状不能被干净地推翻,那么就回到普通的副代理人身上, 并用对立的提示。

#### 跨模式升级

 一个单一模式的审查者与原作者分享盲点 — — 一个更冷,不同结构的模型抓住了它们。 怀疑驱动已经是非三角决定的选择,因此在这个范围内提供跨模式是技能价值的一部分,而不是可选的摩擦。

**交互式会话: 总是提供。 永远不要默默跳过 。**

**步骤1:询问用户**

 在以上步骤3的单模型审查后,但在RECONCILE之前,暂停并询问:

> *"单模式审查完成,需要跨模式第二意见吗?选项: Gemini CLI, Codex CLI,手工外部审查 ( 你把它贴到别的地方),或者跳过。"*

这个问题在每一个互动的疑惑循环中都是强制性的——即使是在感到低沉的文物上也是如此,使用者——而不是代理者——决定成本是否值得,代理者的工作是表露选择.

**步骤2:如果用户选择一个 CLI - 核实,然后援引**

1. 检查工具在 PATH (`which gemini`, `which codex`).
2. 试一下能行 (`gemini --version` 或相当)  在通过全速提示之前——一个 Stale 或破碎的二进制符可能通过 `which`  但实际输入失败 。
3. 与用户确认确切的引用, 包括所需的旗帜、 认证和 env vars ( 例如, API 密钥)执行方式各有不同;永远不要假设。
4.  通过ATIFACT + Contract + 对抗性提示 **仅限**。没有会话背景,没有索赔。
5.  如果文物里有引文 `$(...)`,或者背书,更喜欢sdin (`echo … | gemini`) 或内嵌的异端文件 `-p "…"`。在有疑问时,请用户在运行前确认引用。
6. 将输出带入步骤 4 (复原).

**永远不要把文物插进空壳引用的论据中.** 代码、减值和审查提示 通常包含反键, `$(...)`,并引用将跳出提示或执行嵌入式 shell 的字符。将全部提示写入一个文件,并通过 stdin 管道它。

示例形状 (对照已安装的工具校验旗帜——不同执行和版本的语法不同):

```bash
# Write the adversarial prompt + ARTIFACT + CONTRACT to a temp file first.
# Then pipe via stdin so shell metacharacters in the artifact stay inert.

# Codex (read-only sandbox keeps the CLI from writing to your workspace):
codex exec --sandbox read-only -C <repo-path> - < /tmp/doubt-prompt.md

# Gemini ('--approval-mode plan' is read-only; '-p ""' triggers non-interactive
# mode and the prompt is read from stdin):
gemini --approval-mode plan -p "" < /tmp/doubt-prompt.md
```

 只读的沙盒是装货的细节:怀疑文物本身可能包含说明书 (故意或意外即时注射) 横跨的模型 CLI  否则会针对您的工作空间执行。

**步骤3:如果 CLI  不可用或失败**

明确显示失败。 提供 : 手动运行, 尝试不同的工具, 或跳过 。 不要默默地回到单模式上 —— 用户应该知道跨模式没有发生 。

**步骤 4: 如果用户跳过**

 确认输出中的跳过 (*"只有单一模式的发现才能成功"*) 继续恢复。跳伞是好的; 静默跳伞不是好的。

** 非互动背景** (CI, `/loop`自动启动, 预定运行):

- 跨型号是 **跳过**,而跳跃必须是 **已公布**  在输出中: *"交叉模型跳过:非交互上下文".*
- **永远不要援引外部 CLI 未经用户明确授权** - 这是一个装填的安全财产。

跨模型会增加成本、耐久性和工具脆弱性。 代理商会显示每个周期的选择; 用户会决定这种文物是否值得。

### 步骤4:复原——收回的粗略调查结果

 审查者的产出是数据,而不是判决. ** 你还是管弦乐的** 对照每个发现重读文物文本后再进行分类——给审查者打上橡皮印章,与忽略它一样是失败模式.

 对于每个发现,在此分类 **优先排序** ( 第一匹配类胜出):

1. **合同误读** ——审查员特别标出某件东西,因为你提供的合同合同不明确或不完整,首先确定合同,在下一个周期重新分类。
2. **有效 + 可诉** 真正的问题需要改变 改变它,重新打开。
3. **有效的权衡** ——问题确实存在,但修复成本超过接受成本,明确记录取舍,让用户看到。
4. **噪声** ——评论员在上下文下标出了实际上正确的东西,评论员没有. 注意,继续前进,并问:在合同中加上上下文会阻止假旗吗?

新的评论员可能因为缺乏上下文而是错误的,不要因为"新鲜"而推迟.

### 步骤5:停止——环线断裂,不重复

停止时 :

- 下一次重复只返回微不足道或已经考虑过的调查结果, **或**
- 完成3个周期 (升级为用户, 不要单独磨掉第四个), **或**
- 用户明确说"飞船"

 如果在3个周期之后,审查者仍然出现实质性问题,那么文物可能还没有准备好。 向用户说明这一点 — 三个尚未解决的周期是有关文物的信息,而不是继续循环的理由。

 如果3循环"明显不足",因为文物很大:文物太大了——返回第二步并分解,不要抬起束缚.

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "我有信心,跳过怀疑的台阶" | 信心与新问题的正确性关系不大,确定性的时刻正是盲点隐藏之时。 |
| "勾搭审查员很贵" | 调试生产中的错误行为要花费更多。 检查是受约束的; 错误不是 。 |
| "评论员会只是挑剔" | 将提示限制为"根据合同,签发会导致这种失败". |
| "我最后会怀疑" `/review`" | `/review`  当课程校正费用低廉时, 疑惑会早发现错误的方向。 到公关时已经太晚了。 |
| "如果我怀疑每一步,我永远不会飞船" |  该技能适用于非三角决定,而不是每个按键键. Re-re-read "When Not to Use". |
| "两种观点总比一种好" | 反省一下,不要迟缓 |
| "评论员不同意,所以我错了" | 评论员缺乏你的背景——分歧是信息,不是判决,重新阅读文物,分类,然后决定. |
| "跨模型总是更好的" | Cross-model catching blind space a model one share with itself, 但它增加了成本和工具脆弱性。 提供每个交互的怀疑周期 — 由用户决定是否该文物值得使用 。 代理的任务就是浮出水面,而不是打开它。 |
| "用户答应过一次, 所以我可以继续援引 CLI" |  每次引用都是它自己的授权,文物,即时,旗帜在呼叫之间变化——在每次运行前与用户重新确认确切的指令. |

## Red Flags


-  为一行重命名或格式化的更改注入新文本审查器
-  在不重新阅读文物文本的情况下, 将审查器输出视为权威
- 循环 > 3 循环而不升级到用户
- 以"这样好吗?" 而不是"发现问题" 来提醒评论员
-  在时间压力下对高决定跳过怀疑
-  在未变的文物上重新喷发新文字 ( 你会得到同样的发现 你在拖延时间)
- **怀疑剧场 ( 可核对信号)**: 在两个或两个以上的周期中, 被审查者显示出实质性结果, 零结果被归类为可执行结果。 您正在验证而不是怀疑。 停止并升级 。
- 只有在犯罪后才会怀疑... `/review`,而不是由怀疑驱动的发展
- 外部编码硬化 CLI 引用时未与用户确认工具存在, 已配置, 并接受准确的语法
- **默默地跳过交互疑惑循环中的交叉型号.**  即使不推荐,提议也必须是可见的。跳过是好的;沉默跳过不是的。
-  当一个外人悄悄地退后 CLI 错误或缺失 —— 显示失败并让用户重定向
- 将合同从审查员的投入中删除
- 将索赔转交审查员 ( 对协议的偏见)

## 与其他技能的互动

- **`code-review-and-quality` / `/review`**: 互补。 `/review` 使用这两个方法。
- **`source-driven-development`**: SDD 核实 * 关于框架的事实*  对官方文件的质疑进行验证 * 你对文物的推理*. SDD 检查一下 API 已存在; 您在合同中正确使用了怀疑驱动的检查。
- **`test-driven-development`**: TDDRED步骤是被怀疑是混凝土——失败的测试是不可抗拒的尝试。 TDD 应用, 失败的测试 * 为:* 行为主张的疑惑步骤.
- **`debugging-and-error-recovery`**:当审查者浮出一个真正的故障模式时,掉入调试技能以本地化和固定.
- **重播协奏规则** (`../../references/orchestration-patterns.md`): 这种技巧是主会话的管弦乐。一个叫另一个人的人是反模式的B —— 参见上面的加载限制。

## Verification


 在应用怀疑驱动的发展之后:

- [ ] 每一项非部落决定 ( 根据上述定义) 被明确列为索赔,然后才成立。
- [ ]  每件非三角文物至少一份新内容审查 (测试失败 : TDDRED 步骤满足了行为要求,每个与其它技能的互动)
- [ ]  审查员收到了ARTIFACT + Contract——不是索赔,也不是你的推理
- [ ] 复审官的告示是对抗性的 ("发现问题"),不验证 (好吃吗?)
- [ ] 调查结果与文物文本进行了分类 ( 未加橡胶印章) 使用优先权:合同误读/可诉/权衡/噪音
- [ ] 满足了停车条件 (微小的发现, 3 个周期, 或用户覆盖)
- [ ]  在交互模式下,跨模型是 **明确提议** 切换到用户 (不论文物价值多少) 并在产出中确认了答复。
- [ ]  在非交互模式下, 跨模型被跳过并宣布跳过
- [ ] 任何外部 CLI 引用前先是PATH检查,工作二进制测试,与用户的语法确认,并明确授权运行
