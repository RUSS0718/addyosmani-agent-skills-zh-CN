---
name: constraint-driven-development
description: Establishes a project's quality bar as a written contract and stops agents quietly lowering it. Interviews the user on which dimensions matter, supplies sane default thresholds when they have no number in mind, records everything in CONSTRAINTS.md, and watches the diff for a weakened bar — new @ts-ignore or eslint-disable suppressions, skipped or deleted tests, assertions stripped out, unimplemented stubs, thresholds edited down. Use when no quality bar is written down, when the user says "set up constraints" or "define our standards", when the user wants dimensions they care about — accessibility, web performance, coverage — set up as enforced constraints, when an agent keeps silencing checks or skipping tests to get to green, when you need a coverage or performance threshold and don't know what number to pick, or when an agent writes more code than anyone will read.
---

# 约束性驱动开发

## Overview


 这包里的其他技能描述了什么是好的外观. `code-review-and-quality`  给你五把斧头。 `test-driven-development` 让你有一个循环。 `security-and-hardening` 这一切都会让特工读到 可能跟不上 也可能跟不上 没有一个能活到会场结束

 这种技能产生了不同的东西: **这个项目的** 栏,数字,它比对话长,可以机械地检查。

原因很重要。 当您写代码时, 读它会告诉你它是否很好。 一个特工在下午写得比你那个星期会读的还要多, 所以判断会从你的脑袋里移出, 进入绕圈的检查。 这些检查需要存在, 它们需要您实际选择的号码, 并且需要发射足够接近该特工修复自身输出的工作 。

斯佩克驱动的发展说明了什么是建设。 测试驱动的发展证明了它行之有效。 约束驱动的发展定义了“足以运送”的含义,在有人在拉力请求中对此提出争论之前。

## When to Use


在下列情况下应用此技能:

- 启动一个工程或一个重要功能, 没有写质量栏
- 用户要求"设置约束","添加质量门","确定我们的标准",或"停止代理运输垃圾".
-  一个代理正在生成一个音量 没有人在逐行读取
- CI  有支票,但没人能说 哪些阻止合并,哪些是装饰
- 覆盖面、性能或可获取性数字被质疑为每份PR,而不是一次性决定
-  你马上就要逃跑了 `/build auto` 或任何自主循环, 唯一站在它和主 之间的东西是测试套房 代理也写

** 不使用时 :**

-  该项目已经有一个 `CONSTRAINTS.md` 用户没有改变它, 读它,然后跟着它
- 一次性的脚本,钉子,丢弃的原型
- 用户现在想要一个代码审查 (`code-review-and-quality`) 或一个 CI 管道建造 (`ci-cd-and-automation`)
- 产品前市场适配码,预计寿命为两周——下面的楼层仍然值得,其余的则不值得.

## 装入限制

采访需要现场用户. ** 别在非互动背景下运行它** (CI, `/loop`,自动运行) 如果缺少约束而你身处其中之一, 请应用下面的地板, 注意你做了, 并标注其他的人。

## 进程

###  第1步:在问起前检测

永远不要问你能读到什么,在第一个问题之前,集合:

| 什麽? | 向甚么处寻. |
|------|---------------|
| 语言和堆栈 | `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml` |
| 测试运行器 | 依附关系, `test` 脚本, 已有的测试文件 |
| 现有接口 | `eslint.config.*`, `biome.json`, `.ruff.toml` |
| 覆盖今天 | `coverage/` 输出, 或运行套件一次 |
| CI | `.github/workflows/`, `.gitlab-ci.yml` |
| 代理牵引器 | `.claude/`, `.codex/`, `AGENTS.md` |

报告你在两行中发现的东西,然后只问剩下的是什么.

### 步骤2:四个问题,每个问题都有默认

遵守一次问起的纪律 `interview-me`,有一个变化:这里每个问题都有默认,所以"我不知道"是一个完整的答案,它仍然产生一个工作配置.

```
Q1: Beyond the floor, which of these do you want enforced?
    (a) Test coverage on new code
    (b) Security scanning
    (c) Performance budgets
    (d) Accessibility
    (e) Architecture boundaries
GUESS: (a) and (b) — you have a test runner already and you're handling user input.
DEFAULT if unsure: (a) and (b).
Say what each pick costs: (c) and (d) need a running URL, (e) needs a rules file written.
```

```
Q2: When a check fails while the agent is mid-task, should it block or warn?
GUESS: Block. You're running agents unattended and a warning nobody reads is a warning.
DEFAULT if unsure: Block on the floor, warn on everything else for the first two weeks.
```

```
Q3: Do you have target numbers in mind, or should I measure where you are today and hold that line?
GUESS: Measure. Most teams don't have a number, and an invented one gets ignored.
DEFAULT if unsure: Measure and hold. See "Ratchets" below.
```

```
Q4: What's the slowest check you'll tolerate before the agent hands work back?
GUESS: About 90 seconds. Longer and you'll stop running it.
DEFAULT if unsure: 90 seconds at task end, unlimited in CI.
```

停止在四点。十二问题摄入 产生一个没人理解的假象 和一个后悔开始的用户。

###  第3步: 写成 . md

 在 repo 根处有一个文件。 任何牵引器上的代理都可以读取, 更改它会出现在它所属位置的检讨中 。

```markdown
# Constraints

Last reviewed: 2026-08-08 by @addy

## Floor (always enforced, no setup required)

- No new suppression comments: `@ts-ignore`, `eslint-disable`, `# noqa`, `# type: ignore`
- No unimplemented stubs: `throw new Error("Not implemented")`, empty `catch {}`
- No skipped or deleted tests without a reason in the commit message
- No secrets in source
- This file does not get weakened to make a change pass

## Enforced with numbers

| Dimension | Rule | Checked by | Runs at |
|-----------|------|-----------|---------|
| Types | Zero type errors | `tsc --noEmit` | every edit |
| Lint | Zero errors from our config | `biome check` | every edit |
| Secrets | No secrets in source | `gitleaks detect --redact` | every edit |
| Coverage | Changed lines ≥ 80% covered | `vitest run --coverage` + git diff | task end, CI |
| Security: code | No high findings | `semgrep scan --config p/default` | CI |
| Security: deps | Nothing at high or above | `osv-scanner scan source -r .` | CI |
| Accessibility | Zero critical or serious | `axe $PREVIEW_URL --tags wcag2a,wcag2aa,wcag21aa` | preview deploy |
| Performance | LCP ≤ 2500ms, CLS ≤ 0.1 | `lighthouse $PREVIEW_URL --output=json` | preview deploy |

Every row names the command that produces the verdict. A dimension with a
number and no command in this column is an aspiration, not a constraint.

## Measured, not yet enforced

| Metric | Today | Direction |
|--------|-------|-----------|
| Project coverage | 62.4% | must not fall |
| Bundle size (main) | 184 kB | must not grow |

## Exceptions

| ID | Rule | Path | Reason | Owner | Expires |
|----|------|------|--------|-------|---------|
| W1 | `no-explicit-any` | `src/legacy/**` | Rewrite tracked in ENG-441 | @addy | 2026-11-01 |
```

 然后添加一行到 `AGENTS.md` 财务报告和财务报告 `CLAUDE.md`: `Read CONSTRAINTS.md before writing code. Do not weaken it to make a change pass.`

### 步骤4:安装每个维度所需的设备

选择一个维度意味着安装某种东西。不要让用户留下一个数字,没有机制,不要在事实上存在一个时发明自己的检查器——这些工具之所以被列出,是因为其规则格式和阈值是生态系统目标中其他所有的东西,所以团队现有的配置继续工作.

| 尺寸 | 工具 | 安装 | 运行 | 门打开 |
|-----------|------|---------|-----|---------|
| 类型 (TS 翻译:) | 缩写 | 已经在那里 | `tsc --noEmit` | 任何错误 |
| 类型 (Python) | 神秘点 | `pip install mypy` | `mypy .` | 任何错误 |
| 林特语Name |  您的现有配置 | 已经在那里 | `eslint .` / `biome check` / `ruff check` | 任何错误 |
| 覆盖范围 (联合来文) |  你的试跑者 | 已经在那里 | `vitest run --coverage` (或 `jest --coverage`) | 变更线路的覆盖范围 |
| 覆盖范围 (Python) | pytest-cov (英语:Pytest-cov) (英语). | `pip install pytest-cov` | `pytest --cov --cov-report=lcov` | 不变 |
| 安全:代码 | Semgrep 语录 | `pipx install semgrep` | `semgrep scan --config p/default --config p/owasp-top-ten` | 任何高的发现 |
| 安全:秘密 |  ⁇  | `brew install gitleaks` | `gitleaks detect --redact --no-banner` | 任何调查结果 |
| 安全:依赖性 | Osv 扫描器 | `brew install osv-scanner` | `osv-scanner scan source -r .` | 高或以上 |
| 性能: 页 | 灯塔 | `npm i -D lighthouse` | `lighthouse $URL --output=json --quiet` | LCP, CLS, 性能分数 |
| 性能: 捆绑 | 大小限制 | `npm i -D size-limit` | `size-limit --json` |  每字节预算 |
| 无障碍 | 轴核 | `npm i -D @axe-core/cli` | `axe $URL --tags wcag2a,wcag2aa,wcag21aa` | 零临界或严重 |
| 建筑 | 依赖-克虏赛尔 | `npm i -D dependency-cruiser` | `depcruise --validate src` | 任何违反行为 |
| 担保质量 | 斯特雷克语Name | `npm i -D @stryker-mutator/core` | `stryker run --mutate <changed files>` | 变异分数 |

5件东西,如果你跳过它们,会咬你:

1. **`--redact`  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}这可不是可选的**  没有它, 代理记录中对应的秘密区域, 即泄露的密钥最终会变成日志、 概要或承诺信息。 报告规则和位置, 永远不要显示值 。
2. **灯塔和斧头需要 URL.**  它们只针对运行中的应用程序工作, 所以它们属于运行时阶段, 相对于预览部署或本地服务器, 您先启动。 如果工程没有 URL 击球——a CLI,一个库,一个桌面应用程序——说到这个并放下维度,而不是发明一个不能运行的支票.
3. ** 把那些昂贵的都放进潜水器** `stryker run --mutate` 在整个 repo 上需要几个小时才能关闭; 在文件上, 需要一分钟才能触摸到变化。 Semgrep 也是如此, 它需要路径列表 。
4. **覆盖范围不需要第二次测试。** 读你的套房已经写好了 和它交叉 `git diff`运行两次的套房 得到一个数字 是最快的方式让人们讨厌这一点。
5. **Semgrep的登记规则可以自由运行;在重新分配之前检查许可证。** `opengrep` 是一个与规则格式相同的即时叉,并且 JSON  如果这对你的法律团队很重要,则输出。

 每个都添加到项目本身的脚本中,所以在没有代理的情况下可以复制:

```json
{
  "scripts": {
    "check:fast": "tsc --noEmit && eslint . && gitleaks detect --redact --no-banner",
    "check:task": "npm run check:fast && vitest run --coverage",
    "check:full": "npm run check:task && semgrep scan --config p/default && osv-scanner scan source -r ."
  }
}
```

绘制地图比工具更重要。 `check:fast`  是编辑后运行的, `check:task`  当经纪人认为它完成了, `check:full` 输入 CI.

命令现在生活在两个地方—— `Checked by` 列内 `CONSTRAINTS.md` 和这些脚本。 `CONSTRAINTS.md`  是空洞源 : 它与每个命令并列并列, 并在检讨中出现。 脚本是方便的包装符, 必须镜像它, 而不是第二个真理源; 如果它们漂移, 文件会获胜 。

###  第5步: 将它连接到生命周期

 一个最大的错误就是到处跑。一个检查会拖住经纪人的关机,一个关机门的人比没有关机门更糟糕,因为酒吧看起来还是存在的。

| 阶段 | 命令 | 运行中 | 预算 |
|-------|---------|-----------|--------|
| 布隆迪 | `/build` | 类型、油漆、秘密、地板 | 5s以下,仅更改文件 |
| 事实 | `/test` | 相关测试,变更线路覆盖. | 90岁以下 |
|  审查 | `/review` |  所有东西,加上下面的警卫 | 分钟 |
| 船舶 | `/ship` | 方向检查,无回归 | CI |

两项规则 保持这个可容忍的:

1. **diff的范围。**  请检查所触动的行, 而不是全部回波。 已更改行的覆盖是代理可以移动的号码; 项目覆盖是它继承的号码 。
2. **费用决定安置。** 超过几秒钟的任何东西都会从编辑循环中移出。在整个repo上进行突变测试需要几个小时;在文件上,一个变化在一分钟内被触动,这就是一个检查运行和一个不运行的检查运行之间的区别。

###  第6步:保护酒吧本身

 有人会指出,如果代理员写代码和支票,支票证明什么也没有,一半正确,值得在周围做工程.

特工们不会设计聪明的漏洞,他们撞上了一张红色支票,走最便宜的路走向绿色。在检阅时,注意这五个动作:

1. **门槛移动了。** 预算降低,重度下降,快速阶段的支票取消。 `CONSTRAINTS.md` 与其在分支点的状态相对应。
2. **测试变得更加容易了。** `.skip` 加上,一个测试文件被删除, 断言退出了测试 留下来。
3. ** 一个检查员被打消了声.** 新设 `@ts-ignore` 或 `eslint-disable`4次压制值得特别注意,因为它们关闭了一张你所依赖的支票: `istanbul ignore` 代号从覆盖范围掉下来 而不是测试它, `Stryker disable` 隐藏了一个幸存的变种人, `nosemgrep` 财务报告和财务报告 `gitleaks:allow` 这么做是为了安全调查
4. **工作尚未完成。** 扔的树枝,空的 `catch` 将失败变成沉默,a `TODO` (c) 将执行情况置于何处。
5. **出现了例外。**  没有人讨论过例外表中的新一行。

这一切都不需要工具 超越 `git diff`紧紧的酒吧应该安静;松紧的酒吧应该响亮。

与编号维度不同的是,楼层本身没有事实上的工具,因此要求执行的代理人往往会从头写一个检查器,两个代理人写两个不同的检查器。 [references/floor-guard.md](references/floor-guard.md) (diff-scoped, 退出 `0`/`1`/`2` 每个生态系统的适应性模式)适应它,而不是重塑它, 出于同样的原因,每个维度 命名一个事实上的工具: 所以这个机制是相同的 横跨和堆栈。

**并非所有的检查都是循环的。** 给他们排上一个问题: 特工能通过写出不起作用的代码来通过吗?

- **外部** - 轴-核心编码WCAG, `osv-scanner` 灯塔测量的是真正的浏览器 代理商不能和这些东西争吵
- ** 项目**  你的规矩,你的分层界限,文件是人类拥有的
- **套间**  你自己的测试 最有用 也是唯一真正的循环测试

完全属于第三类的栏杆,其价值低于一个有外部意见的栏杆。 请检查是否存在至少一个外部约束。

### 第七步:拉切兹,当你没有号码时

 在62%的密码库上设定80%的覆盖率,你得到一个永远的红色建筑,然后一个学习忽略红色建筑的团队.

选项要求不做决定: 记录你在哪里, 然后拒绝恶化。 把它放在“ 计量的, 尚未执行的” 表格中, 加上今天的数字和方向。 每一次检查都比照记录的数值, 而不是一个愿望。 当数字改善时, 更新它; 当数字下降时, 这就是结果 。

这也是对训练的公平反对。 模型会因为通过测试而得到奖励,你可以在数秒内评估。 建筑腐烂在数月内出现,从未达到重量。 鼠标就是缺失的罚局,写在建筑物能看到的地方。

## 模拟默认值

 当用户没有意见时,请使用这些,它们被选择在第一天被大多数代码库所满足.

| 约束 | 默认 |  为什么这个号码 |
|------------|---------|-----------------|
| 变更项目覆盖面 | ≥ 80% | 高度足以强制测试, 低足以允许配置线 |
|  项目覆盖面 |  今天的价值,千万不要掉下来 | 无需通过任何论据 |
| 变异分数 ( 如果使用的话) | + 60% 开始 | 典型的套房以前从未变异;80%成熟 |
| 依赖性脆弱性 | 高或以上没有 |  这下面主要是噪音 |
| LCP 常规程序 |                                     | Core Web Vitals "好"的门槛 |
| CLS 密码 | ≤ 0.1 | 一样 |
| 无障碍 |  * 无重大或严重违规事件 | 中小的往往值得商榷 |
| 例外寿命 | 90天时间 | 足够长的时间来计划修补 足够短的时间来回忆 |
| 轮椅耐受性 | 0.5% |  当一个无关文件移动数字时, 吸收会漂移 |

将数字和原因合并起来。一个没有理由的阈值会被下一个击中它的人删除。

## 升级路径

制约作用在牙齿的三层,从头开始。

1. **仅写.** `CONSTRAINTS.md`  没有成本,没有发现诚实的错误,依赖于代理遵守。
2. **编著.**  一个 `npm run check` (或 `make check`) 运行快速检查, 窃听你的经纪人的后 编辑钩和你的 CI. 决定性,没有新的依赖性。
3. **工具支撑.**  一个处理 diff 范围界定、 预算、 鼠标和 守护检查的专职导师。 当配置输出 shell 脚本时使用。 下层守护符的引用 。 [references/floor-guard.md](references/floor-guard.md)  这是一半的警卫检查的起点。

多数项目在2号时应该停止,在保持约30多行的检查运行外壳时,移动到3号.

**第一次运行只能是地板。** 地板守卫只使用diff,不需要安装,因此您可以在第一天执行地板,并在安装每个工具时添加编号的维度,而不是在第一个任务保护之前站起每个检查器。安装全机的安全工具 ( ⁇ , osv -扫描器) 也可以运行 CI- 只要你宁愿保持笔记本电脑的干净; `Runs at` 栏中。

## Common Rationalizations


|  对不起 | 现实 |
|--------|---------|
| "一旦密码定下来,我们就会增加限制" | 代码在移动时被允许的东西周围解决 |
| "测试是限制" |  你写的测试证明你同意自己的观点, 他们说不涵盖新代码,依赖风险, 或捆绑增长。 |
| "我们无法达到80%的覆盖率" |  那就不要设定80%,设定今天的号码并按住 |
| 这会拖住经纪人的脚步 |  除非你把慢检查放进快速循环中。这是放置错误,不是反对限制的争论。 |
| "我会记住我们的标准" | 代理商不会的,它正在写 大部分代码 |
| "约束会阻碍我们的航运" |  除了一个主人和一个约会可以解禁你。删除约束可以解禁所有人永远 |

## Red Flags


 如果发现,请停止并重新考虑:

- 采访经过了四个问题, 或者产生了一个用户无法解释的配置
- 预算已经确定 代码库今天失败了 没有计划到达它
-  一个维度被写入了 INTRAINTS.md 中,其中包含一个数字,但后面没有工具
-  当一个事实上的棋子存在时,一个棋子被手卷起来,所以球队现有的配置被忽略了.
- 每一个限制都由项目自己的测试套房检查,没有外部意见
- `CONSTRAINTS.md` 与失败的特性相同
- 例外没有主人,或者超过一年期满
- 代理商提议放松门槛 而不是固定代码
- 慢速检查在编辑循环中着陆, 有人开始通过 `--no-verify`
-  没人开过门 `CONSTRAINTS.md` 因为它是写

## Verification


 该技能在下列情况下得到正确应用:

- [ ] `CONSTRAINTS.md` 存在,每个数字都有明确的理由
- [ ] 地板是执行的,在目前的密码库上通过,不作改动
- [ ] 用户选择的每个维度都安装了工具, 今天运行的命令
- [ ]  每个限制都说它运行在哪里, 快速的舞台停留在几秒内
- [ ] 至少有一个限制是外部的 ( 未通过该项目本身的测试判断)
- [ ] 仅测量的度量衡记录了今天的价值和方向
- [ ] 例外有所有权人和终止日期
- [ ] `AGENTS.md` 或 `CLAUDE.md`  文件的点数
- [ ] 在当前分支上运行的试运行不会产生用户不同意的失败

## 另见

- `interview-me` ——这种技能的收录借用的一问一问的学科.
- `code-review-and-quality` 如何审查;这种技能决定审查执行什么
- `ci-cd-and-automation` 建造管道,这些制约因素贯穿于
- `test-driven-development` 覆盖范围和突变限制措施的套间
- `security-and-hardening` - 安全方面应包含哪些内容
- `performance-optimization` ——性能数字来源于何处.
