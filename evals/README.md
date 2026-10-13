# 技能 Evals

 这种再投资如何衡量其技能是否实际起作用: **触发**  当他们应当, **保持区分** 彼此之间,和 **更改代理行为** 每一个技能承诺的方式。

##  前文艺术 (和我们通过的东西)

 没有单一的定居社区标准来评估 `SKILL.md` 技术,但有两种方法可以导致:

- **Anthropic技能创造器 v2** 定义每个技能 `evals.json` (提示 + `expectations[]` 从笔录中分级) 加上对照样本提示对描述进行触发精确测试。 [`evals.json` 计划](https://github.com/anthropics/skills/tree/main/skills/skill-creator)  对于我们的行为层次, 并添加一个可选 `kind` 字段来选择被分级的文物。
- **超级力量** (欧巴) 测试技能,使用击打+ `claude -p` + 快速固定和分级脚本。 我们的行为跑者遵循同样的无头-`claude` 样式,并带有从 `expectations[]`.

两者都不提供的是 **决断性, CI- 安全吗?** 检查多技能 *目录* ——每个技能的描述是否包含词汇用户实际说的, 并做两个技能的描述碰撞?下面是第二级,这是这个repo的加法.

## 三级

| 级别 | 检查什么? | 运行 | 费用 |
|---|---|---|---|
| 1. 结构 |  前题、命名、要求的章节、命令等同 | CI (`validate-skills.js`, `validate-commands.js`) | 免费 |
| 2. 触发和路由 | 阳性提示将他们的技能排在顶端 K ; 阴性提示不会; 没有两种描述接近碰撞 | CI (`run-evals.js`) | 免费 |
| 3. 行为 | 随从技能的代理人满足其需要 `expectations[]` | 应要求 (`run-evals.js --behavioral`) | 键 |

 第二级为a **词典近似** 路线 (生成 TF- IDF 超过描述)。它不能判断语义学——即第3级的工作——但它抓住了主导真实触发错误的两个失败模式:一个描述缺少词汇用户说 (假阴性),以及一个超广的描述, 排名高于正确的技能 (假阳性)。第二级失败通常意味着 *修改描述*而不是爱华。

## 运行

```bash
# Tier 2 — deterministic, runs in CI
node scripts/run-evals.js
node scripts/run-evals.js --min-rank1 95  # enforce the current routing floor

# Tier 3 — behavioral, runs each eval through headless claude, then grades it
node scripts/run-evals.js --behavioral test-driven-development            # spends tokens
node scripts/run-evals.js --behavioral test-driven-development --dry-run  # prints the plan only
```

3级支持两种行为类文物. `execution`  是默认: 每个 eval 运行在一个丢弃的 git 寄存器中, 真正的工程输入来自 `files[]` 已实现 `evals/fixtures/` 并且以他为基准, `--output-format stream-json --verbose` 执行追踪,包括工具调用. `dialogue`  可以交付的技巧是对话本身; 不需要固定, 且分级者不要求文件编辑或命令就判断助理的谈话转折。 Claiming `dialogue` 是一种经人审查的豁免,而不是一般的越狱舱门,用于执行技能.

执行器使用明确的许可模式运行 (`--permission-mode acceptEdits` 加上一个预先核准的工具列表)  因此执行 evals 可以真正编辑文件, 运行命令, 检查 diffs, 并做出承诺而不是被拒绝和描述 。 追踪被围起来, 作为不可信的数据, 在分级器中提示, 并在 stdin 上管道到分级器 。 ( 它们可以是兆字节; argv 会击中 OS 参数大小限制),执行器和分级器的调用将超时,分级器的输出被验证为 JSON  在写入之前 `evals/results/` (已丢弃)  在技能创造者那里 `grading.json` 纪律技能还包括时间压力、沉没成本和权威压力等压力案例;这些技术可以证实,当提示人提出跳过时,工作流程仍然维持。

## 插件 evals (Claude Code)

`claude plugin eval` (Claude Code 2.1.269 或以后日期) 装入整个插件,每个技能同时运行大小写 `evals/plugin/`,然后用插件和不插件对每个案件进行评分。它补充了第二级,而不是替换它:第二级检查一个描述是否带有词汇用户说;这个检查是 Claude Code自己的路由器在自然提示上选择技能 以及技能对回答的附加内容 (编号 `Δ` 栏目).

```bash
claude plugin eval . --no-publish                                          # every case, both arms, 3 runs each
claude plugin eval . --case code-review-fires --runs 1 --ablation none     # one cheap iteration on one case
```

案件是目录控件 `prompt.md` ( 前题:转弯和工具限制;正文:即时,即时,即时发送) 和每个文件的1个分级 `graders/`报告 `evals/plugin/results/<timestamp>/` 每一次跑步和每一次跑步 `llm` 校友是名副其实的模特儿 所以这是在需求下进行的 从来不是 CI 就像3级一样

 有两条规矩要诚实 名单是... `experimental.evals` 密钥范围扫描到 `evals/plugin/`,所以一个固定的别处下 `evals/` 从来没有被读作案件。 `tool_used: Skill` 年级生只说技能是否被发射;在双臂运行中,它被排除在分数之外,所以在回答本身上用一个年级生来配对. `code-review-fires` 显示模式: 点火指示器, 免费 `regex` 寻找技能严重性分类的年级生 (危急、必要、耐特、可选、考虑、报告) 用作标题或标签,以及 `llm` 法官在布置一个。 `code-review-stays-quiet` 准确性检查:审查技能不得对先行试验的请求开火,而且 `Δ` ;第二个没有记分的指标记录是否为 test-driven-development 而不是开火。

初学是怎样的 (Claude Code 2.1.278, 阴茎-5). 技能引用有条理:7分之5中发射的审查技能在案件所用措辞上运行, test-driven-development 7分之2发射,试射第一,请读 `Δ` 和报表而不是退出代码,或通过 `--threshold 0.8`  作为 CI  在一次运行中,审查技能没有开火,而且答复中仍然使用其严重程度分类法作为标题,没有基线在九次运行;加载的技能描述即使在没有引用的情况下也可能塑造答复。描述问题,而描述是杠杆。在原始描述中,“在我合并之前,请检查一下这个“diff”;“在我合并之前,请检查一下这个“拉我”的更改”;“请检查这个“拉我”在进入主机之前”很少使用该技能;在模型中,它自己审查了“diff”的特性,但没有判定,严格等级或五轴传。说明技能增加的是什么没有帮助,也没有命名“diff”和“拉请求”本身;区别在于,即使将“diff”粘贴在线上,也可以适用,而模型却视之为“我自己即可这样做。” 在模型中,Claude-opus-5 5 中,有一个条款将四个审查语句从27个火中7个移到2个火中,从9个火中最接近的9个火中,它必须在2个火中移动到2个火中。 `code-review-stays-quiet-on-commit-message` 守护此条款:贴有承诺信息请求的粘贴diff不得解雇复核技能。从当天起的两个实用规则:针 `--model` 并记录 Claude Code  当您报告数字时版本, 因为 CLI 更新自身,默认模式在早晚之间两次改变; 给一个案件10或10个以上的转弯预算, 因为模式可能通过斜线命令达到技能 (`agent-skills:review`,则 `code-review-and-quality`),这需要两个转弯。

## Eval 大小写格式

 每个技能一个文件 : `evals/cases/<skill-name>.json`.

```json
{
  "skill_name": "test-driven-development",
  "trigger": {
    "positive": [
      { "prompt": "Write a failing test for this bug before fixing it", "top_k": 3 }
    ],
    "negative": [
      { "prompt": "Update the architecture diagram in the docs", "owner": "documentation-and-adrs" }
    ]
  },
  "evals": [
    {
      "id": 1,
      "kind": "execution",
      "prompt": "Finance filed the reconciliation bug written up in BUG.md. Fix it.",
      "expected_output": "A failing reproduction test for the lost-cent case, a fix preserving both README invariants (exact sum, earliest-shares fairness), the fairness invariant covered by its own test, full suite passing",
      "files": [
        "test-driven-development"
      ],
      "expectations": [
        "A test reproducing the lost-cent case from BUG.md is added and shown failing before src/split.js is modified",
        "The final implementation satisfies the full README fairness invariant (leftover cents go to the earliest shares): splitCents(10000, 3) returns [3334, 3333, 3333] as BUG.md expects and splitCents(100, 7) returns [15, 15, 14, 14, 14, 14, 14] as the README example shows; dumping the whole remainder on a single share would violate both",
        "The fairness invariant from the README has its own test case in the suite on an input with remainder of at least 2 (such as splitCents(100, 7)), where dumping the whole remainder on one share would fail it, beyond the reported lost-cent case",
        "The full suite is run with the repository's own command after the fix"
      ]
    }
  ]
}
```

- `evals[]` 使用技能创造器的核心方案 (`id`, `prompt`, `expected_output`,可选 `files[]`, `expectations[]`)  加此寄存器的可选 `kind`. `kind` 必须是 `execution` 或 `dialogue` 默认为 `execution` 。执行 evalls 需要非空 `files[]`; 路径相对 `evals/fixtures/` 对话框 evals 可能会省略 `files[]`  因为笔录是文物,期望是可核查的声明,是分级员对照相关文物——行为,而不是用词检查的.
- `trigger`  这是Repo的扩展。 `positive` 提示是现实的用户询问此方向 (`top_k` 默认为 3; 技能签名时收紧为 1 。). `negative` 属于 a *不同* 技能;不能将这种技能排在他们的首位。 `owner`  在可以时:跑者声称拥有者 **居次数**  这种技巧,将阴性变成真正的双向路由测试,而不是当瞬间不匹配时可以空转的测试.

**写入好触发提示 :** 解释用户实际交谈的方式; 不复制描述 ( 这是游戏的eval) 如果一个现实的提示不能排位,因为描述缺少其词汇,这就是一个真实的发现——改进描述.

## 增加一个技能

每一个有 eval 文件的技巧船。当你添加时 `skills/<name>/`,添加时 `evals/cases/<name>.json` 至少有3个正触发器, 2个负触发器, 和 1 个行为 eval 。 执行 eval 必须有 `evals/fixtures/<name>/`; 使用 `kind: "dialogue"`  只有技能的可交付性才是真正的对话本身。缺少案件文件、不完整的案件数、未知类型、无效的固定路径,以及缺少必要的固定装置。 CI 错误。

##  要观察的量表

Tier-2 运行打印 a **触发级别-1率** (分享将技能排在首位的正提示,而不仅仅是顶级K). CI 运行与 `--min-rank1 95`,将有用的头室留在100%基准线以下,因此不相关的描述编辑不会立即转动 CI 红色 。 随着路由的改善, 升起地板; 永远不要降低地板, 以便通过回归。 下降的数字表示描述相互飘移。 碰撞检查错误在% 75% 的双向描述上相似, 警告在% 50% 。 当这些值显示时会有一个描述- vobulary 差距 。 ( 见 [#351](https://github.com/addyosmani/agent-skills/issues/351) 用于原始示例),修正描述,而不是提示。
