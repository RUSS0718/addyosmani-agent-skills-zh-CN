#  从代理技能入手

代理技能与任何接受的 AI 编码代理合作 Markdown  本指南涵盖了通用方法。关于特定工具的设置,请参见专用指南。

 在建立自己的项目之前,想要一个实例吗?
[交互式辅导](https://skills.addy.ie/tutorials/) 穿过一个
绿地建筑 棕地特色 安全自动化环路
 可复制的提示 Claude Code, Codex,或任何其他代理人。

## 技能如何运作

每种技能都是个 Markdown  文件 (`SKILL.md`) 描述一个特定的工程工作流程。当装入代理上下文时,代理遵循工作流程——包括核查步骤、避免的反标和退出标准。

**技能不是参考文献。** 它们是物剂所遵循的逐步过程.

## 快速启动 (任何探员)

### 1. 清除储存库

```bash
git clone https://github.com/addyosmani/agent-skills.git
```

### 2. 选择技能

浏览 `skills/`  目录。每个子目录包含一个 `SKILL.md` 改为:
- **何时使用** ——表示这种技能的触发器适用.
- **进程** ——分步骤工作流程.
- **核查** ——如何确认工作完成.
- **共同合理化** - 代理可能用来跳过步骤的借口
- **红旗** - 标志技能被侵犯

### 3. 将技能装入您的代理人

复制相关内容 `SKILL.md` 内容包含在您的代理系统的即时、 规则文件或对话中。 最常见的方法 :

**系统提示 :**  在会话开始时粘贴技能内容 。

**规则文件 :**  在您的工程规则文件中添加技能内容 (CLAUDE.md, .cursorrules等 互联网档案馆的存檔,存档日期2013-12-21.).

**对话 :**  在给指令时参考技能: "遵循 test-driven-development  此更改的流程".

### 4. 必要时使用元技能进行发现

 如果您的代理商没有本地技能, 请从 `using-agent-skills`  它包含一个流程图,将任务类型映射到适当的技能。

 如果您的主机已经从描述中发现并激活了技能, 请不要粘贴 `using-agent-skills` 输入一个总是运行的系统即时文件或规则文件。这为同一任务创建了两个路由器。安装单个技能,让主机按要求激活它们。

### 现有项目不需要迁移

使用正常设置路径从已有工程的根安装包
用于您的代理, 然后继续在此工程中工作 。 技能激活以匹配
任务;它们不需要新的寄存器布局或一次性转换
已存在的代码。

 不复制此寄存器根 `AGENTS.md` 或 `CLAUDE.md` 进入项目。
这些文件为代理技能本身配置了贡献者。只添加技能
以及您代理人通常读作的任何特定项目指令。
 在既定的密码库中推出,遵循 [采纳指南](adoption-guide.md).

##  建议设置

跑到一个真正的项目? **[采纳指南](adoption-guide.md)** 包括两个端到端路径:一个绿地项目从第一天开始的整个生命周期,以及一个已建立代码库的递增的,验证的第一个推出。下面的设置是快速版本。

### 最小数 ( 从这里开始)

将三种基本技能装入您的规则文件:

1. **spec-driven-development** - 用于界定要建设的
2. **test-driven-development** - 为了证明它有效
3. **code-review-and-quality** ——用于合并前验证质量.

 这三项内容涵盖AI协助开发中最关键的质量差距.

###  全生命周期

 就全面覆盖而言,按阶段分列的载荷技能:

```
Starting a project:  spec-driven-development → planning-and-task-breakdown
During development:  incremental-implementation + test-driven-development
Before merge:        code-review-and-quality + security-and-hardening
Before deploy:       shipping-and-launch
```

### 上下文预览加载

 不要同时装入所有的技能——它浪费了上下文。装入与当前任务相关的技能 :

- 进行中 UI装入 `frontend-ui-engineering`
- 调试中? 装入 `debugging-and-error-recovery`
- 设置 CI装入 `ci-cd-and-automation`

## 技术解剖学

 每个技能都遵循相同的结构:

```
YAML frontmatter (name, description)
├── Overview — What this skill does
├── When to Use — Triggers and conditions
├── Core Process — Step-by-step workflow
├── Examples — Code samples and patterns
├── Common Rationalizations — Excuses and rebuttals
├── Red Flags — Signs the skill is being violated
└── Verification — Exit criteria checklist
```

 见 [技术-外科](skill-anatomy.md) 完整规格。

## 使用代理人

 那个... `agents/`  目录包含预配置的代理人物 :

| 探员 | 目的 |
|-------|---------|
| `code-reviewer.md` |  五轴码检讨 |
| `test-engineer.md` | 测试策略和写作 |
| `security-auditor.md` | 脆弱性检测 |
| `web-performance-auditor.md` | Core Web Vitals 绩效审计( P) (途经 `/webperf`) |

 当您需要专业审查时加载代理定义。例如,请您的编码代理“使用代码审查代理人物审查此修改”,并提供代理定义。

## 使用命令

 那个... `.claude/commands/`  目录包含斜线命令,用于 Claude Code:

| 命令 | 技能被援引 |
|---------|---------------|
| `/spec` | spec-driven-development |
| `/constraints` | constraint-driven-development |
| `/plan` | planning-and-task-breakdown |
| `/build` | incremental-implementation + test-driven-development |
| `/build auto` | planning-and-task-breakdown → incremental-implementation + test-driven-development (整个计划,一个批准) |
| `/test` | test-driven-development |
| `/review` | code-review-and-quality |
| `/code-simplify` | code-simplification |
| `/ship` | shipping-and-launch |
| `/webperf` | 网络业绩审计员 (专家代理, 只使用网络应用程序) |

> **说明:** 安装为 a Claude Code 插件, 您可能看到一个警告, 如
> _默认命令/ 文件夹被忽略, 因为显示的集“ 命令 ”_.
>  这是预期的。 根 `commands/`  目录属于 Antigravity CLI
> 并故意与 `.claude/commands/`全部数据。 Claude Code 斜线
> 命令正确装入 `.claude/commands/`警告是化妆品

## 使用参考文献

 那个... `references/`  目录载有补充清单:

| 参考文献 | 使用方式 |
|-----------|----------|
| `testing-patterns.md` | test-driven-development |
| `performance-checklist.md` | performance-optimization |
| `security-checklist.md` | security-and-hardening |
| `accessibility-checklist.md` | frontend-ui-engineering |
| `definition-of-done.md` |  所有技能/每一变化 |
| `observability-checklist.md` | observability-and-instrumentation |
| `orchestration-patterns.md` | doubt-driven-development |

需要超出技能范围的详细图案时装入引用 。

 如果你安装一个技能 `npx skills add ... --skill <name>`中,只有
选中 `skills/<name>/`  目录已复制。 技能仍然有效, 但是路径
补充清单中的补充清单 `references/`  目录为
 不可用 。 使用全检索集成、 复制寄存器或复制
需要的核对表 `references/` 已安装技能内的目录。
 这一可移植性差距在以下表格中跟踪:
[addyosmani/agent-skills#361](https://github.com/addyosmani/agent-skills/issues/361).

## 光谱和任务文物

 那个... `/spec` 财务报告和财务报告 `/plan` 命令创建工作文物 (`SPEC.md`, `tasks/plan.md`, `tasks/todo.md`)。视其为 **生活证明书** 这项工作正在进行中:

-  在开发期间保持它们的版本控制,这样人和代理就有一个共同的真理来源.
- 范围或决定发生变化时更新。
-  如果您的 repo 不想要这些长期文件, 请在合并或添加文件夹之前删除它们 。 `.gitignore` 工作流程并不要求它们是永久性的。

### 闭会期间的工作

相同的工艺品是会话之间的交接。对于一个小任务,在一次会话中运行整个生命周期。对于任何非会话,每一次会话都会是新鲜的。 (Spec 计划 建设 审查 .) 保持上下文的焦点——将工作推向前进的是核准的文件,而不是对话:

- 说明- `SPEC.md`,或者在你的光谱实际生活的地方
- `tasks/plan.md` 财务报告和财务报告 `tasks/todo.md` ——或者计划识别的外部跟踪器,如果使用一个

**切换前**,确保这些文件反映仍然适用的决定、你批准的范围、问题仍未解决、下一个任务和目前的核查状态 (测试结果是什么?).

** 在新会议上**,阅读实际文件并查看 `git status`  不要假设你无法在文物中看到批准。 将记录的“ 测试通过” 当作对特定基线的主张: 重新运行它所覆盖的检查,如果代码已经移动,如果它没有说出什么是运行与它覆盖的区域,或者如果你即将触摸它覆盖的区域。 如果基线仍然保持, 接受它并着手完成下一个任务—— 关键是检查与变化的大小成正比,而不是每次交接时的全套套房。

#### 任务边界重新启动和拉尔夫环路

`/build auto` 它可以在一个会话中运行整个批准的计划。它不需要或执行每个任务的新进程。它的每个任务状态更新,验证结果,以及承诺使每个已完成的任务成为可重启的边界,所以一个有能力的外部控制器可以在那里退出并恢复,而不取决于聊天历史。

shell 级的“ Ralph 循环” 是管理行为,而不是单独的技能工作流程。 如果您使用, 只有在当前任务到达了已记录的边界后才能重新启动; 在返回时, 在选择下一个待完成的任务之前读取耐用文物和寄存器状态。 进程退出不是任务通过的证据, 重新启动不能绕过批准闸门 。 请参见 `context-engineering` 技术 ** 可恢复的会话边界** 用于交割清单的章节。

 这不需要的 `/spec` 财务报告和财务报告 `/plan` 包装器——任何代理机构,包括a `npx skills add` 安装只有技能的:

> 读取 SPEC.md,然后以接受标准和依赖命令将其拆解为可核查的小任务。 tasks/plan.md 财务报告和财务报告 tasks/todo.md. 还没有产品代码——先告诉我计划.

> 阅读SPEC.md, tasks/plan.md 财务报告和财务报告 tasks/todo.md,然后检查事情的实际站姿—— `git status`, 加上重新运行记录的核查状态不再覆盖的检查。告诉我下一个未检查的任务和任何尚未打开的任务,然后停止:在您开始前,我会确认范围。如果计划看起来不完整,请说明所缺少的东西,而不是重写它。

## 提示

1. **开始于 spec-driven-development** 任何非部落性工作
2. **总是装入 test-driven-development** 写入代码时
3. ** 不要跳过核查步骤** - 他们才是重点
4. ** 有选择地装入技能** - 更多背景并不总是更好的
5. **使用代理审查** ——不同观点抓住不同问题.
