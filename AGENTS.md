# 助理人员.md

 此文件为 AI 编码代理提供了指导 (Claude Code, Cursor,副驾驶,反重力等.)  当使用此寄存器中的代码工作时。

> **范围:**  此文件将配置正在运行的代理程序 [`addyosmani/agent-skills`](https://github.com/addyosmani/agent-skills)  它不是要复制到其他项目或全球代理配置中;可重复使用的资产是技能。 `skills/`,而不是这个文件。

## 仓库概览

技能汇编 Claude.ai 和 . .ai 和 .ai 和.ai .ai 和.ai .ai Claude Code  对高级软件工程师来说,技能是可扩展的包装指令和脚本 Claude  还有你的编码代理能力

## OpenCode 一体化

OpenCode 使用a **技术驱动的执行模式** 由电源 `skill` 工具与此寄存器 `/skills`  目录。

### 核心规则

-  如果任务匹配技能, 您必须引用它 。
- 技能设置于 `skills/<skill-name>/SKILL.md`
-  如果某项技能适用,就永远不要直接执行
- 总是完全遵守技能指示 ( 不部分应用它们)

### 意图 —— 技能测绘

代理商应当自动映射用户对技能的意向:

- 特性/新功能  `spec-driven-development`,则 `incremental-implementation`, `test-driven-development`
- 规划/细目 `planning-and-task-breakdown`
- 错误/ 失败/ 出乎意料的行为    `debugging-and-error-recovery`
- 守则审查 • `code-review-and-quality`
- 重构/简化  `code-simplification`
- API 或接口设计 + `api-and-interface-design`
- UI 工作 工作 —— `frontend-ui-engineering`

### 寿命周期绘图 (隐性命令)

OpenCode  不支持斜线命令, 如 `/spec` 或 `/plan`.

 相反,毒剂必须在内部遵循这一生命周期:

- 国 务 院 `spec-driven-development`
- 计划 ∗∗ `planning-and-task-breakdown`
- 布隆迪                      `incremental-implementation` + `test-driven-development`
-  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}真的吗? {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}真的 `debugging-and-error-recovery`
-  审查 ∗∗ `code-review-and-quality`
- 船舶 − `shipping-and-launch`

### 执行模式

 对于每项请求:

1. 确定是否适用任何技能 ( 甚至1%的机会)
2. 使用 `skill` 工具
3. 严格遵循技能工作流程
4. 仅按规定步骤着手实施 (谱,计划等.) 已经完成

### 反合理化

 下列想法是不正确的,必须予以忽视:

- "这太小了,对技能来说"
- "我可以迅速执行这个"
- "我会先收集背景"

正确的行为 :

- 总是先检查和使用技能

 这样可以保证 OpenCode 行为类似 Claude Code 完全执行工作流程。

## 管弦乐团:人物、技能和指挥

 此 repo 有 3 个 堆肥层 。 它们有不同的工作, 不应该混淆 :

- **技能** (`skills/<name>/SKILL.md`) 具有步骤和退出标准的工作流程。 *怎么样*意向匹配时的强制跳跃。
- **个人** (`agents/<role>.md`) - 以视角和产出格式发挥作用。 * 谁*.
- **斜线命令** (`.claude/commands/*.md`) ——面向用户的切入点. *何时*。 。 。 。 。

组成规则: **用户 (或斜线命令)  是管弦乐的,人不会引用他人的。** 一个人可以援引技能。

 这个Repo认可的唯一多人管弦乐模式是 ** 用合并步骤平行扇出** 用于: `/ship` 运行 `code-reviewer`, `security-auditor`,以及 `test-engineer` 同步并综合他们的报告。不要构建一个“路由器”人物,来决定该呼叫的其他人;这就是斜线命令和意图映射的任务。

 见 [docs/agents.md](docs/agents.md)  关于决定矩阵和 [references/orchestration-patterns.md](references/orchestration-patterns.md)  为完整的图案目录。

**Claude Code 间断 :** 个人在 `agents/` 工作为 Claude Code 子剂 (自动从此插件中发现 `agents/`  目录) 并且作为特工团队的队友 (产卵时按名称引用)。两个平台限制与我们的规则一致:子剂不能产生其他子剂,而团队不能筑巢。插件剂沉默地忽略了 `hooks`, `mcpServers`,以及 `permissionMode`  前题字段。

## 创建新技能

> **开始前:** 运行飞行前的检查 [捐款额(美元)](CONTRIBUTING.md#before-proposing-a-new-skill),搜索目录,检查打开的公关 (`gh pr list --state open`),确认这个想法符合 [docs/skill-anatomy.md](docs/skill-anatomy.md),并证明您在 PR 描述中存在空白。大多数新技能的想法与现有的技能或开放的 PR 重复; 倾向于扩展现有的技能,而不是添加近似重复。 Confilmbulting.md 是这个工作流程唯一的真理来源。

 这回波的技巧是先降级的: `skills/<kebab-case-name>/SKILL.md` 与 YAML  前题 (`name`, `description`) 并跟随剖析 (概览、使用时间、过程、共同合理化、红旗、核查)添加一个 `scripts/` 仅当技能船可操作的帮助者; 大多数技能只被标记为减值, 没有每个技能的拉链包。

 关于完整格式、命名惯例、前题规则、辅助文件阈值和书写原则,见 [docs/skill-anatomy.md](docs/skill-anatomy.md),这是技能结构的唯一真理来源。请不要在此重复该指导,并与之链接。
