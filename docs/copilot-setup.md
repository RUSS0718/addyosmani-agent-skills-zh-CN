# 使用代理技能与 GitHub Copilot

 本指南涵盖 VS Code。用于独立的 `copilot` 命令行工具,参见 [副驾驶-cli-setup.md](copilot-cli-setup.md).

**安装能给你带来什么?** 技能。每个安装的技能成为以前题命名的斜线命令 `name` — `/spec-driven-development`, `/test-driven-development`,等等。 (笑声) `npx skills add addyosmani/agent-skills` 和下面的手动拷贝只安装技能。这两条路线都没有复制Repo的短寿命周期包装符 。 (`/spec`, `/plan`, `/build`, `/test`, `/review`, `/ship`) - 这些都是... Claude Code 居中命令 `.claude/commands/`。使用完整的技能名称,或添加自己的别名——参见 [生命周期工作流程](#lifecycle-workflows).

> **`/spec`, `/plan`, `/build`, `/test`, `/review`, `/code-simplify`, `/webperf` 财务报告和财务报告 `/ship` 从未出现在副驾驶。**  他们是 Claude Code 命令,副驾驶不读 `.claude/commands/`. 安装后没有看到它们,而不是缺少步骤。 `/spec-driven-development` (单位:千美元) `/spec`, `/planning-and-task-breakdown` (单位:千美元) `/plan`,等等; 完整的映射在 [工作周期流量](#lifecycle-workflows)打开 VS Code  您可以添加 [简短的别名](#optional-short-spec-style-aliases)  如果你想要简表的话,你自己。

## 设置

### 副驾驶指令

副驾驶员支持使用 `.github/skills`, `.claude/skills`,或 `.agents/skills` 位于您的仓库中的目录。

```bash
mkdir -p .github/skills/test-driven-development .github/skills/code-review-and-quality

# Create files for essential skills
cat /path/to/agent-skills/skills/test-driven-development/SKILL.md > .github/skills/test-driven-development/SKILL.md
cat /path/to/agent-skills/skills/code-review-and-quality/SKILL.md > .github/skills/code-review-and-quality/SKILL.md
```

 无论你用哪条路线,结果都是一 `SKILL.md`  根据这三条工程路径中的一条,每个技能目录。安装者运行一个 global/user 旗子写到别的地方 —— 检查它的输出路径 。 运行 `/skills`  在“副驾驶聊天”中打开 **配置技能** 菜单,确认发现了什么。

详情请参见: [创造代理技能 GitHub Copilot](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-skills)  页:1 VS Code 指南 [代理技能](https://code.visualstudio.com/docs/agent-customization/agent-skills).

### 代理人物 (*特勤员)

代理驾驶员支持专门的代理人格。使用代理技能代理:

> **重要内容 :** GitHub Copilot 需要自定义代理文件命名 `*.agent.md`.
> 名为文件 `*.md` 被副驾驶默默忽略了
>  见 [VS Code  自定义代理文件](https://code.visualstudio.com/docs/copilot/customization/custom-agents#_custom-agent-file-structure) 详细情况。

```bash
# Create the agents directory and copy agent definitions
mkdir -p .github/agents
cp /path/to/agent-skills/agents/code-reviewer.md .github/agents/code-reviewer.agent.md
cp /path/to/agent-skills/agents/test-engineer.md .github/agents/test-engineer.agent.md
cp /path/to/agent-skills/agents/security-auditor.md .github/agents/security-auditor.agent.md
```

 在副驾驶聊天中调用代理 :
- `@code-reviewer Review this PR`
- `@test-engineer Analyze test coverage for this module`
- `@security-auditor Check this endpoint for vulnerabilities`

###  自定义指令 (用户级别)

在所有寄存器中要的技能:

1. 打开 VS Code  * 设置 * GitHub Copilot − 自定义指令
2. 添加您最常用的技能摘要

##  建议配置

### .github/copilot-instructions.md

GitHub Copilot  通过下列方式支持项目级别指示: `.github/copilot-instructions.md`.

```markdown
# Project Coding Standards

## Testing
- Write tests before code (TDD)
- For bugs: write a failing test first, then fix (Prove-It pattern)
- Test hierarchy: unit > integration > e2e (use the lowest level that captures the behavior)
- Run `npm test` after every change

## Code Quality
- Review across five axes: correctness, readability, architecture, security, performance
- Every PR must pass: lint, type check, tests, build
- No secrets in code or version control

## Implementation
- Build in small, verifiable increments
- Each increment: implement → test → verify → commit
- Never mix formatting changes with behavior changes

## Boundaries
- Always: Run tests before commits, validate user input
- Ask first: Database schema changes, new dependencies
- Never: Commit secrets, remove failing tests, skip verification
```

### 专门人员

 在"副驾驶聊天"中使用代理来进行有针对性的审查工作流程.

## 工作周期流量

技能默认可以被用户引用,所以一旦发现,整个生命周期都可以在技能自己的名下使用——没有额外的配置:

| 工作流程 | 副驾驶引用 |  页:1 |
|----------|--------------------|-------|
| 定义 | `/spec-driven-development` |  在代码前写入结构化的光谱 |
| 计划 | `/planning-and-task-breakdown` | 生产 `tasks/plan.md` 财务报告和财务报告 `tasks/todo.md` |
| 构建 | `/incremental-implementation` | 与 `/test-driven-development`; 一次一个切片 |
| 校验 | `/test-driven-development` | 红绿源, 为错误提供 Prove- It |
|  审查 | `/code-review-and-quality` |  五轴审查 |
| 船舶 | `/shipping-and-launch` | 发射准备 |

类型 `/` 浏览实际装入这个工作空间的东西。

自然语言是倒置,不管斜线命令是否出现,在任何Copilot表面都起作用:

> 使用该 spec-driven-development 技能来写入 spec for [特性].

> 使用该 code-review-and-quality  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}能回顾一下我所演的变迁 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你觉得呢?

###  可选:短 `/spec`- 风格化名

VS Code扩展主机本地代理支持 [提示文件](https://code.visualstudio.com/docs/agent-customization/prompt-files) 时间: `.github/prompts/<name>.prompt.md`,每个变成一个 `/<name>` 斜线命令。 代理主机有 ** 没有** 使用即时文件——那里,使用上面的技巧名称.

化名是一条技能的细长的捷径, 仅此而已。 这些化名不会执行此 repo 中的管弦法 。 Claude Code 命令 - `/build auto`单项批准计划和执行的通行证和 `/ship`'平行人物 a fan-out。 复制在 Copilot 上是它自己的作品, 而不是一个单文件的别名所覆盖的东西。 偏好将技能名称作为您的默认; 只有在短表值额外文件时才能为别名所覆盖 。

创建一个, 不对已有文件乱划 :

```bash
mkdir -p .github/prompts
[ -e .github/prompts/spec.prompt.md ] || cat > .github/prompts/spec.prompt.md <<'EOF'
---
description: Write a structured spec before writing code
---

Use the spec-driven-development skill.

Ask clarifying questions about the objective and target users, core features and
acceptance criteria, stack preferences and constraints, and known boundaries.
Then write a spec covering objective, commands, project structure, code style,
testing strategy, and boundaries. Save it as SPEC.md in the project root and
confirm with me before any code is written.
EOF
```

重新装入窗口, 然后键入 `/spec`.

其余的,运行一样 `mkdir`/`cat` 与文件名互换的块 ( 文件名 * 为:* 命令名称),并带走 `description` 和尸体从这个桌子。 **将整个机体替换如下: `---`  前题**,不仅仅是技能名称——Spec body的SPEC.md和澄清问题指示只属于定义,而把它们留在其中将产生 `/plan` 或 `/test` 写一个字段。

|  别名文件 | `description` | 完整身体——前题之下的一切 |
|------------|---------------|--------------------------------------------------|
| `.github/prompts/plan.prompt.md` | 将批准的任务分解为命令的可核查任务 | 使用该 planning-and-task-breakdown 。读取规格,然后将工作分成小的、可独立核查的任务,每个任务都有接受标准和明确的依赖顺序。将结果保存到 `tasks/plan.md` 财务报告和财务报告 `tasks/todo.md`. 不写产品代码——告诉我计划,等待我的批准. |
| `.github/prompts/build.prompt.md` | 执行下一个计划的任务,先测试 | 使用该 incremental-implementation 财务报告和财务报告 test-driven-development 技能。阅读 `tasks/plan.md` 财务报告和财务报告 `tasks/todo.md`,然后选择下一个未选中的任务,并且只选择那个任务。先写一个失败的测试,让它通过,重构,运行套件,并勾选任务。停止那里并报告所发生的变化。 |
| `.github/prompts/test.prompt.md` |  在满足他们的代码前写测试 | 使用该 test-driven-development  对于新行为,请写一个失败的测试,在任何执行之前抓取它。对于一个错误,请先用失败的测试复制它,然后修复它。每一步后运行套件,然后显示红色和绿色输出。 |

自己写尸体,或者从桌子上拿走,而不是复制 `.claude/commands/*.md` 逐字记录:这些文件的参考技能 `agent-skills:<name>` 页:1 Claude Code 插件命名空间, 表示对 Copilot 没有任何意义 。

###  如果技能的斜线命令不出现

 首先排除了预期的大小写: 如果您唯一错过的命令是短寿命周期包装器 (`/spec`, `/plan`, `/build`, `/test`, `/review`, `/ship`),没有什么不对的,这些是: Claude Code 命令,绝不在副驾驶中浮出水面 ( 在顶端看到音符)。以下步骤用于技能本身的名称,例如: `/spec-driven-development`也不见了

工作通过这些顺序—— `/spec-driven-development` 失踪和 `/spec` 失踪的原因不同:

1. **检查技能落地.**  每一份都需要自己的目录,其中包含: `SKILL.md`。此指南使用的项目级路径是: `.github/skills/`, `.claude/skills/` 财务报告和财务报告 `.agents/skills/` -但安装器运行时有 global/user 旗子写在工作空间外 (`~/.agents/skills/` 及同等数额),所以在其中任何一个都不存在的技巧可能只是亲自安装。 [VS Code 代理技能](https://code.visualstudio.com/docs/agent-customization/agent-skills),然后重新安装到项目中,如果您想要项目范围。
2. **检查前题.** `name` 必须是在场的,并且是有效的——它 * 为:* 斜线命令。其前题选择退出用户引用的技巧将不会出现 。
3. ** 请检查配置技能菜单 。** 运行 `/skills` 并在此确认该技能已启用 。
4. **开始新的会话。**  新加入的技巧并非总能拿起中场对决.
5. **检查一下主机**  若为: `/spec-driven-development` 工作不过你 `/spec`  别名不会,你很可能在代理主机上,它不读即时文件.
6. **检查版本.** VS Code (**帮助 ~ 关于**) 和“副驾驶聊天”扩展名;技能-slash-commands是最近才出现的,所以如果两者之一都落后,则更新。

##  用法提示

1. **保持指令简洁** ——副驾驶指示在突出重点时效果最好. 总结关键规则,而不是包括完整的技能文件.
2. **使用代理审查** ——代码审核员,测试工程师,安全审计代理人为副驾驶的代理模型设计.
3. **聊天中的引用** ——在具体阶段工作时,将相关技能内容粘贴到"副驾驶聊天"上下文.
4. **与公关审查相结合** ——设置"副驾驶",使用代码评审员代理人物a审查公关.
