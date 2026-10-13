# OpenCode 设置

 本指南解释如何使用 Agent Skills 与 OpenCode。可重复使用的资产是: `skills/`  目录; 根 `AGENTS.md`  此寄存器中的文件被重新扫描,不应复制到其他工程中。

## 概览

OpenCode 发现来自多个地点的技能。 Agent Skills 提供两种可选的用法样式:

- **代理驱动工作流程 :**  通过内置自动选择技能 `skill` 工具与项目本地化 `AGENTS.md`  你为自己的仓库写作。
- **命令驱动的工作流程 :** 手动引用生命周期命令 `.opencode/commands/` (选项).

## 安装

 有两种方法可以把技能带入你的项目:

1. 安装于 `skills` CLI ( 快点).
2. 克隆此寄存器并手动复制技能目录 。

 在任一步骤之后, 创建您的本地项目 `AGENTS.md`  如果你想要的话,请抄送 `.opencode/commands/*.md` 文档。

### 备选案文1:安装 `npx skills`

最快的路是开着的 [`skills` CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add addyosmani/agent-skills            # install selected skills
npx skills add addyosmani/agent-skills --list     # browse before installing
```

安装单一技能 :

```bash
npx skills add addyosmani/agent-skills --skill spec-driven-development
```

默认 `npx skills` 安装到工具专用目录 (经常 `.claude/skills/` 或共用地点). OpenCode 将发现技术 放置在那里,因为它读 `.claude/skills/<name>/SKILL.md` 和一般项目 `.agents/skills/<name>/SKILL.md` 路径。

 如果技能落在某个地方 OpenCode  不扫描、复制或链接到下面列出的发现路径之一,例如:

```bash
mkdir -p .opencode/skills
cp -r .claude/skills/<skill-name> .opencode/skills/
```

> **说明:** Per-skill 安装只复制技能目录本身。如果一个技能引用共享文件在 `references/`,将这些复制到已安装的技能目录或安装整个包。见 [#361](https://github.com/addyosmani/agent-skills/issues/361) 用于背景。

### 备选案文2:清除此存储器

1. 清除仓库 :

```bash
git clone https://github.com/addyosmani/agent-skills.git
```

2. 将想要的技能复制到其中之一 OpenCode 技能发现路径。

####  项目-当地安装

```bash
mkdir -p .opencode/skills
cp -r /path/to/agent-skills/skills/<skill-name> .opencode/skills/
```

 例如,安装 `spec-driven-development` 财务报告和财务报告 `incremental-implementation`:

```bash
mkdir -p .opencode/skills
cp -r /path/to/agent-skills/skills/spec-driven-development .opencode/skills/
cp -r /path/to/agent-skills/skills/incremental-implementation .opencode/skills/
```

####  全球安装

```bash
mkdir -p ~/.config/opencode/skills
cp -r /path/to/agent-skills/skills/<skill-name> ~/.config/opencode/skills/
```

#### 交叉兼容路径

OpenCode 也发现被安排在 Claude- 兼容或通用的代理路径:

- `.claude/skills/<name>/SKILL.md`
- `~/.claude/skills/<name>/SKILL.md`
- `.agents/skills/<name>/SKILL.md`
- `~/.agents/skills/<name>/SKILL.md`

 如果你已经分享技能了 Claude Code 财务报告和财务报告 OpenCode,任何这些地点的工作。

###  要复制什么

复制目录到 `skills/` (举例来说, `skills/spec-driven-development/`)。每个目录必须包含一个 `SKILL.md`  文件。不复制寄存器根 `AGENTS.md` 或 `CLAUDE.md`;这些文件配置了此寄存器本身的开发。

##  项目 `AGENTS.md`

创建一个 `AGENTS.md` 输入 ** 您的自有项目** root。这是系统提示 OpenCode 时间和如何引用已安装的技能。 `AGENTS.md` 输入 `addyosmani/agent-skills`,此文件属于您的项目, 并且应该适应您的堆栈 。

 下面是您可以粘贴到您的项目的模板 `AGENTS.md`:

```markdown
# Agent Skills (OpenCode)

This project uses skills installed under `.opencode/skills/` (or a compatible path).

## Core Rules

- If a task matches a skill, invoke it with the `skill` tool before acting.
- Skills are located in `.opencode/skills/<skill-name>/SKILL.md`.
- Follow the skill workflow strictly; do not partially apply it.
- Never skip required steps such as spec, plan, or test when a skill demands them.

## Intent → Skill Mapping

Map the user's intent to the matching skill automatically:

- Feature / new functionality → `spec-driven-development`, then `incremental-implementation` and `test-driven-development`
- Planning / breakdown → `planning-and-task-breakdown`
- Bug / failure / unexpected behavior → `debugging-and-error-recovery`
- Code review → `code-review-and-quality`
- Refactoring / simplification → `code-simplification`
- API or interface design → `api-and-interface-design`
- UI work → `frontend-ui-engineering`

## Execution Model

For every request:

1. Determine if any skill applies (even a small chance).
2. Load the skill with `skill({ name: "<skill-name>" })`.
3. Follow the skill workflow exactly.
4. Only proceed to implementation once required steps are complete.
```

 另存为 `AGENTS.md`  在您的项目根中。 OpenCode 将自动加载。

> **说明:** 词根 `AGENTS.md` 内部 `addyosmani/agent-skills` 寄存器是供在此寄存器上工作的撰稿人使用的,不应复制到其他项目。 [捐款额(美元)](../CONTRIBUTING.md#repo-scoped-files).

## 如何运作

### 1. 技术发现

OpenCode 沿着以下路径走 ( 项目-先是当地,然后是全球):

- `.opencode/skills/<name>/SKILL.md`
- `~/.config/opencode/skills/<name>/SKILL.md`
- `.claude/skills/<name>/SKILL.md`
- `~/.claude/skills/<name>/SKILL.md`
- `.agents/skills/<name>/SKILL.md`
- `~/.agents/skills/<name>/SKILL.md`

每种技能必须包含: `SKILL.md` 带有有效文件 `name` 财务报告和财务报告 `description`  在它的前题。

### 2. 自动调用技能

 当您的项目 `AGENTS.md` 指示代理使用技能,代理评价每个请求并将其映射到适当的技能.

实例:

- "打造一个特色" ~ `incremental-implementation` + `test-driven-development`
- "设计一个系统" ~. `spec-driven-development`
- "修复一个错误" ~ `debugging-and-error-recovery`
- "审查这个代码" ~ `code-review-and-quality`

### 3. 寿命周期测绘 (隐性命令)

OpenCode 不需要斜线命令,但是如果您更喜欢看到下一节。在代理驱动模式下,生命周期被默认地映射:

- 国 务 院 `spec-driven-development`
- 计划 ∗∗ `planning-and-task-breakdown`
- 布隆迪                      `incremental-implementation` + `test-driven-development`
-  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}真的吗? {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}真的 `debugging-and-error-recovery`
-  审查 ∗∗ `code-review-and-quality`
- 船舶 − `shipping-and-launch`

### 复制可选斜线命令

 如果您更喜欢明文命令, 请将实例命令文件从此寄存器复制到您的工程中, 并调整它们以引用您安装的技能 :

```bash
mkdir -p .opencode/commands
cp /path/to/agent-skills/.opencode/commands/*.md .opencode/commands/
```

> **说明:** 存储器目前不包括 `.opencode/commands/*.md` 打开 `main`。您可以创建自己的命令文件,也可以查看 PR #200, 后者建议添加它们。 一旦它们存在, 上面的图案将适用 。

 一个典型的命令文件看起来像:

```markdown
---
description: Break work into small verifiable tasks
---

Invoke the planning-and-task-breakdown skill. Read the spec and create tasks with acceptance criteria.
```

留着吧 `.opencode/commands/plan.md` 以启用 `/plan` 输入 OpenCode.

##  用法实例

### 例1:特征发展

用户 :
```
Add authentication to this app
```

代理行为 :
- 探测特性工作
- 调用 `spec-driven-development`
-  在写入代码前生成一个规格
- 提高规划和执行技能

### 示例2:错误修正

用户 :
```
This endpoint is returning 500 errors
```

代理行为 :
- 调用 `debugging-and-error-recovery`
- 复制 · 本地化 · 修正 · 增加警卫

### 例3:守则审查

用户 :
```
Review this PR
```

代理行为 :
- 调用 `code-review-and-quality`
- 应用结构化审查 (正确性、设计、可读性等。)

## 代理预期

 为: OpenCode  为了正确工作,代理人应当:

- 总是在动作前检查是否应用了技能
- 使用该 `skill`  在应用时装入技能的工具
- 永远不要跳过所需的工作流程 (光谱,计划,测试等.)
-  不直接跳转到执行中

这些规则由您的项目强制实施 `AGENTS.md`而不是技能的复制品本身。

## 限制

- 技能的引用取决于模式的遵守。
- OpenCode  不自动安装技能; 复制或安装所需的目录。
-  如果一个技能引用文件在下面 `references/`,在手动安装时可能需要复制这些内容。

##  目 录

1. 安装您需要的技能, 无论是与 `npx skills add addyosmani/agent-skills`  或者从此寄存器的克隆复制到 `.opencode/skills/` ( 项目), `~/.config/opencode/skills/` ( 全球),或交叉兼容的路径,例如 `.claude/skills/` / `.agents/skills/`.
2. 创建您的本地工程 `AGENTS.md` 上面的规则和意图图。
3. OpenCode 发现技能和你的 `AGENTS.md` 引导代理人引用它们。
4.  可选添加 `.opencode/commands/*.md` 以显示斜线命令。

 这样可以保留可重复使用的资产 (技能) 独立于存储器特定配置 (编号 `addyosmani/agent-skills` 根号 `AGENTS.md`).
