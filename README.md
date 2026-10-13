# Agent Skills

**AI编码代理产品的生产级工程技能.**

技能将高级工程师在构建软件时使用的工作流程、质量门和最佳做法编码。 这些技术被包装后,AI代理商在开发的每个阶段都一致遵循。

## 为什么需要中文版

- 更好适配中文大语言模型，让模型更容易理解工作流、约束和验收标准。
- 方便中文母语开发者阅读、评审、改写和维护 skills。
- 方便接入中文团队的开发流程、代码审查和知识库。

本仓库是 [`addyosmani/agent-skills`](https://github.com/addyosmani/agent-skills) 的中文翻译副本。原仓库的目录结构、代码、配置、命令和 skill 标识符保持不变；文档中的自然语言提供中文译文。术语统一参见[翻译术语表](TRANSLATION-GLOSSARY.md)，重要实现细节请同时对照[原仓库](https://github.com/addyosmani/agent-skills)。

版本变更记录见 [CHANGELOG.md](CHANGELOG.md)，机器翻译范围和限制见 [TRANSLATION-NOTES.md](TRANSLATION-NOTES.md)。

<a href="https://trendshift.io/repositories/25200" target="_blank"><img src="https://trendshift.io/api/badge/repositories/25200" alt="addyosmani%2Fagent-skills | Trendshift" style="width: 250px; height: 55px;" width="250" height="55"/></a>

![爱蒂的 Agent Skills](https://addyosmani.com/assets/images/addys-agent-skills.jpg)

```
  DEFINE          PLAN           BUILD          VERIFY         REVIEW          SHIP
 ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐
 │ Idea │ ───▶ │ Spec │ ───▶ │ Code │ ───▶ │ Test │ ───▶ │  QA  │ ───▶ │  Go  │
 │Refine│      │  PRD │      │ Impl │      │Debug │      │ Gate │      │ Live │
 └──────┘      └──────┘      └──────┘      └──────┘      └──────┘      └──────┘
  /spec          /plan          /build        /test         /review       /ship
```

---

## 命令

9 斜线命令将地图映射到开发生命周期中。每个都自动激活正确的技能 。

|  你们在做什么? | 命令 | 关键原则 |
|-------------------|---------|---------------|
| 定义要构建的内容 | `/spec` | 编码前先写规范 |
| 计划如何建造它 | `/plan` | 小型原子任务 |
| 逐步建设 | `/build` | 一次吃一块 |
| 证明它有用 | `/test` | 测试就是证据 |
| 设置质量栏 | `/constraints` | 做一次决定,到处执行 |
| 合并前的审查 | `/review` | 改善代码健康 |
| 审计 Web 性能 | `/webperf` | 优化前先测量 |
| 简化代码 | `/code-simplify` | 明亮而聪明 |
| 准备发布 | `/ship` | 更快且更安全 |

规范确定后还想减少手动步骤吗？**`/build auto`** 会生成计划，并在一次经批准的运行中执行每项任务——你只批准一次计划，然后它自动运行。它减少了任务之间的人工操作，但不会跳过核查：每项任务仍由测试驱动并单独执行，遇到失败或风险步骤会暂停。

技能也会根据你正在做的事情自动激活——设计 API 会触发 `api-and-interface-design`，构建 UI 会触发 `frontend-ui-engineering`，依此类推。

---

## 快速启动

** 最快速的路径——任何代理,一个命令.** 开放 [技能 CLI](https://github.com/vercel-labs/skills) 安装到 70+ 代理 (Claude Code, Cursor, Codex副驾驶 克莱恩 还有更多):

```bash
npx skills add addyosmani/agent-skills            # install all 25 skills
npx skills add addyosmani/agent-skills --list     # browse before installing
```

 或者抓住个人技能:

```bash
npx skills add addyosmani/agent-skills --skill code-review-and-quality   # five-axis review before merge
npx skills add addyosmani/agent-skills --skill interview-me              # requirements interrogation, one question at a time
npx skills add addyosmani/agent-skills --skill test-driven-development   # red-green-refactor, enforced
```

> **安装一个技能?** 每人一个技能 `npx`  只安装副本
> `skills/<name>/`,而不是还原级别 `references/`  目录。该技能仍然
> 工作,但无法找到补充共享核对表的路径。
> 整个检索集成、复制存储器或将所需核对表复制到
> a `references/`  在已安装的技能内设置目录。此可移植性空白是
> 追踪到 [#361](https://github.com/addyosmani/agent-skills/issues/361).

更喜欢本土融合?

<details>
<summary><b>Claude Code ( 建议)</b></summary>

**市场安装 :**

```
/plugin marketplace add addyosmani/agent-skills
/plugin install agent-skills@addy-agent-skills
```

> **SSH错误?** 市场克隆通过 SSH 重置。 如果您没有 SSH 密钥设置在 GitHub,或者说 [添加您的 SSH 密钥](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) 或使用完整 HTTPS URL 改为强制 HTTPS 市场加固步骤中的克隆:
>  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你觉得呢? {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}我
> /插件市场加 https://github.com/addyosmani/agent-skills.git
> /plugin 安装代理技能@ addy - agent - 技能
> ```
>
>  若为: `/plugin install` 仍然失败 `git@github.com: Permission denied (publickey)`  在 Windows 或 macOS 上,推荐的变通工作是配置 Git 重写一次 GitHub SSH URL 调用 HTTPS 用于子进程克隆 :
>  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你觉得呢? {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}我
> git 配置 - global url. "https://github.com/".insteadOf git@github.com: 请检查access-date=中的日期值 (帮助)
> ```

**地方/发展:**

```bash
git clone https://github.com/addyosmani/agent-skills.git
claude --plugin-dir /path/to/agent-skills
```

</details>

<details>
<summary><b>Cursor</b></summary>

将工作流程技能置于下 `.cursor/skills/` (同步从 `agent-skills/skills/`) 和短期政策 `.cursor/rules/*.mdc` ——不要将全部技能粘贴到规则中。两个目录都存在于您的项目中; 此 repo 不发送 a `.cursor/` 文件夹。见 [docs/cursor-setup.md](docs/cursor-setup.md).

</details>

<details>
<summary><b>Antigravity CLI</b></summary>

安装为技能和子代理的本地插件。在受影响的 Antigravity CLI 版本中，遗留 TOML 命令包装器可能无法被发现；请直接引用基础命名空间中的技能。见 [docs/antigravity-setup.md](docs/antigravity-setup.md#lifecycle-workflows-and-command-compatibility)。

** 从 repo 安装 :**

```bash
agy plugin install https://github.com/addyosmani/agent-skills.git
```

** 从本地克隆安装 :**

```bash
git clone https://github.com/addyosmani/agent-skills.git
agy plugin install ./agent-skills
```

</details>

<details>
<summary><b>Gemini CLI</b></summary>

安装为自动发现的本地技能, 或添加到 `GEMINI.md` 用于持续上下文。见 [docs/gemini-cli-setup.md](docs/gemini-cli-setup.md).

** 从 repo 安装 :**

```bash
gemini skills install https://github.com/addyosmani/agent-skills.git --path skills
```

** 从本地克隆安装 :**

```bash
gemini skills install ./agent-skills/skills/
```

</details>

<details>
<summary><b>风浪</b></summary>

 在 Windsurf 规则配置中添加技能内容。见 [docs/windsurf-setup.md](docs/windsurf-setup.md).

</details>

<details>
<summary><b>OpenCode</b></summary>

复制技能到 `.opencode/skills/` (或 `~/.config/opencode/skills/`),添加项目本地 `AGENTS.md`,并使用内置 `skill`  为代理驱动执行的工具。可选斜线命令可以在 `.opencode/commands/`.

 见 [docs/opencode-setup.md](docs/opencode-setup.md).

</details>

<details>
<summary><b>GitHub Copilot</b></summary>

使用代理定义 `agents/`  作为副驾驶人物和技能内容 `.github/copilot-instructions.md`。寿命周期斜线命令 (`/spec`, `/plan`, `/build`, `/test`, `/review`, `/ship`) 已经 Claude Code 命令,而不在“副驾驶”中出现;按名称引用技能 (`/spec-driven-development`, …) 换成是,看 [docs/copilot-setup.md](docs/copilot-setup.md).

使用独立 `copilot` CLI * 将其安装为插件 — 见 [docs/copilot-cli-setup.md](docs/copilot-cli-setup.md).

</details>

<details>
  <summary><b>Kiro IDE 软件 & CLI </b></summary>
  基罗的技能居于 " 之下 ".kiro/skills/" ,并可在项目或全球级别下存储. Kiro还支持 Agents.md. 见 Kiro Docs at https://kiro.dev/docs/skills/
</details>

<details>
<summary><b>Codex</b></summary>

安装为本地 Codex 插件 (Codex CLI v.0.122+ (中文(简体) ).):

```bash
codex plugin marketplace add addyosmani/agent-skills
codex plugin add agent-skills@agent-skills
```

 第一个命令注册市场;第二个安装插件. Codex 读取根 `skills/` 直接通过目录 `.codex-plugin/plugin.json`。一旦安装,将引用聊天技能 `@` ( 例如, `@spec-driven-development`)。见 [docs/codex-setup.md](docs/codex-setup.md) 用于本地安装和排除故障。

</details>

<details>
<summary><b>命令代码</b></summary>

 用内置安装本地 `cmd skills` 命令。命令代码复制 repo, 发现每个 `SKILL.md`,并安装到 `.commandcode/skills/`:

```bash
cmd skills add addyosmani/agent-skills            # pick skills to install (project)
cmd skills add addyosmani/agent-skills --global   # install for all projects (~/.commandcode/skills/)
cmd skills add addyosmani/agent-skills -s spec-driven-development  # install a specific skill
```

安装的技能出现在TUI斜拉菜单中,例如. `/spec-driven-development`。见 [docs/commandcode-setup.md](docs/commandcode-setup.md).

</details>

<details>
<summary><b> 其他代理人</b></summary>

技能是简单的 Markdown  他们与任何接受系统提示或指令文件的代理人合作。 [docs/getting-started.md](docs/getting-started.md)。安装包但上面没有列出的主机在其中。 [docs/other-hosts.md](docs/other-hosts.md).

</details>



---

## 收 养

已经安装了吗? **[采纳指南](docs/adoption-guide.md)** 涵盖两条路径:从第一天起全生命周期用于绿地项目,或为既定代码库逐步推出首次核查。

---

## 全部25种技能

以上指令是切入点,包括总共25种技能——24种生命周期技能外加: `using-agent-skills` 元技能。每种技能都是结构化的工作流程,包含步骤、校验门和反合理化的表格。也可以直接引用任何技能。

### Meta - 发现哪些技能适用

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [using-agent-skills](skills/using-agent-skills/SKILL.md) | 将收到的工作映射到正确的技能工作流程,并定义共享的操作规则 | 开始一个会话或决定哪种技能适用 |

### 定义 - 澄清要构建的内容

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [interview-me](skills/interview-me/SKILL.md) |  一问一问的访谈 取出用户真正想要的 而不是他们认为他们应该想要的 直到~95%的自信 | 问讯未详,或有用户引用"访我"/"烦我". |
| [idea-refine](skills/idea-refine/SKILL.md) | 结构化 divergent/convergent 考虑将模糊的想法变成具体的建议 |  你有一个粗糙的概念,需要探索 |
| [spec-driven-development](skills/spec-driven-development/SKILL.md) |  在任何代码之前写入涵盖目标、命令、结构、代码样式、测试和边界的 PRD | 启动新项目、功能或重大变化 |
| [constraint-driven-development](skills/constraint-driven-development/SKILL.md) | 专访一个具有正常默认阈值的质量栏, 写作 CONSTRAINTS.md, 按成本排列每个检查, 捕捉代理 静音检查或跳过测试 得到绿色 |  没有标准被写下来, 或代理 生产超过任何人读 |

### 计划 - 打破它

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [planning-and-task-breakdown](skills/planning-and-task-breakdown/SKILL.md) | 将规格分为小的、可核查的任务,并附有接受标准和依赖性命令 |  您需要可执行的单位 。 |

### 构建 - 写入代码

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [incremental-implementation](skills/incremental-implementation/SKILL.md) | 细的垂直切片 - 执行, 测试, 校验, 承诺 。 特性标记, 安全默认, 方便回滚的更改 。 | 任何触及多个文件的更改 |
| [test-driven-development](skills/test-driven-development/SKILL.md) | 红色- 绿色- 试金字塔 (80/15/5)测试大小, DAMP over DRY, Beyonce Rule,浏览器测试 | 执行逻辑、修复错误或改变行为 |
| [context-engineering](skills/context-engineering/SKILL.md) | 饲料代理 正确的信息 在正确的时间 - 规则文件,上下文包装, MCP 一体化 | 开始会话、切换任务或输出质量下降时 |
| [source-driven-development](skills/source-driven-development/SKILL.md) | 将每个框架决定都写进官方文件 - 核查,引用来源,标出未经核实的东西 |  您想要任何框架或库的权威性源代码 |
| [doubt-driven-development](skills/doubt-driven-development/SKILL.md) |  对飞行中的每一项非三边决定进行反常的新文本审查――索赔 ――EXTRACT ――DOUBT ――RECONCILE ――停止,可选用户授权的跨模型升级 | 赌注很高 (生产、安全、不可逆转),在不熟悉的代码中工作,或者一个自信的输出现在比以后调试便宜一些 |
| [frontend-ui-engineering](skills/frontend-ui-engineering/SKILL.md) | 构件结构、设计系统、州管理、响应性设计、WCAG 2.1 AA无障碍 | 建立或修改用户界面 |
| [api-and-interface-design](skills/api-and-interface-design/SKILL.md) | 合同第一设计, Hyrum's Law, 单声道规则, 错误语义, 边界验证 | 设计 API 、 模块边界或公共接口 |

### 校验 - 证明它有效

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [browser-testing-with-devtools](skills/browser-testing-with-devtools/SKILL.md) | Chrome DevTools MCP 实时运行数据 - DOM 检查、控制台日志、网络跟踪、性能剖析 | 构建或调试浏览器中运行的任何内容 |
| [debugging-and-error-recovery](skills/debugging-and-error-recovery/SKILL.md) |  五步之分:繁衍,本地化,减少,固定,守卫. 停止行规则,安全退缩. | 测试失败、 构建中断或行为出乎意料 |

###  审查 - 合并前的质量门

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [code-review-and-quality](skills/code-review-and-quality/SKILL.md) |  五轴审查,更改尺寸 (~100行线),重度标签 (Nit/Optional/FYI),审查速度规范,拆分战略 |  在合并任何更改之前 |
| [code-simplification](skills/code-simplification/SKILL.md) | Chesterton's Fence, 规则500, 减少复杂度, 同时保留准确的行为 | 代码是有效的,但更难阅读或维护 超过它应该 |
| [security-and-hardening](skills/security-and-hardening/SKILL.md) | OWASP  前10名 预防、认证模式、秘密管理、依赖性审计、三级边界系统 | 处理用户输入、认证、数据存储或外部集成 |
| [performance-optimization](skills/performance-optimization/SKILL.md) | 措施第一办法- Core Web Vitals 目标、剖面工作流程、捆绑分析、反模式检测 | 存在性能要求或怀疑回归 |

### 舰只 - 满怀信心地部署

| 技能 |  它会做什么 | 何时使用 |
|-------|-------------|----------|
| [git-workflow-and-versioning](skills/git-workflow-and-versioning/SKILL.md) |  基于中子的开发、原子承诺、变化大小 (~100行线),承诺-as-save-point模式 | 做任何代码更改 (永远) |
| [ci-cd-and-automation](skills/ci-cd-and-automation/SKILL.md) | 左转, 快捷是更安全的, 特性旗、 质量门管道、 故障反馈循环 | 建立或修改建造和部署管道 |
| [deprecation-and-migration](skills/deprecation-and-migration/SKILL.md) | 代码责任思维 强制与咨询折旧 移民模式 僵尸代码删除 | 消除旧系统、迁移用户或日落特征 |
| [documentation-and-adrs](skills/documentation-and-adrs/SKILL.md) | 建筑决定记录, API 文档,内置文档标准 - 文档 * 为什么* | 作出建筑决定、改变API或航运特征 |
| [observability-and-instrumentation](skills/observability-and-instrumentation/SKILL.md) | 结构化伐木、RED 度量衡、 OpenTelemetry 追踪、基于症状的报警 -- -- 工具 | 增加遥测,或运输任何在生产中运行的东西 |
| [shipping-and-launch](skills/shipping-and-launch/SKILL.md) | 发射前核对表,特征旗生命周期,分阶段推出,回滚程序,监测设置. | 准备投入生产 |

---

## 代理人物

目标审查的预配置专家:

| 探员 | 作用 | 视角 |
|-------|------|-------------|
| [代码审查器](agents/code-reviewer.md) | 高级工作人员工程师 |  五轴码检讨有"员工工程师会批准这个标准吗?". |
| [测试工程师](agents/test-engineer.md) | QA专家 | 试验战略、覆盖面分析和 " 证明-It " 模式 |
| [安全审计师](agents/security-auditor.md) | 安保工程师 | 脆弱性探测、威胁建模、 OWASP 评估 |
| [Web 性能审计员](agents/web-performance-auditor.md) | Web 性能工程师 | Core Web Vitals 审计、Quick/Deep 模式和指标诚实规则；通过 `/webperf` 运行 |

 见 [docs/agents.md](docs/agents.md)  对于决定矩阵, 管弦乐规则, 以及人如何组成 技能和斜线命令。

---

## 参考资料核对表

快速参考材料,技能在需要时可加以利用:

| 参考文献 | 封面 |
|-----------|--------|
| [定义定义(done.md)](references/definition-of-done.md) | 与每个任务接受标准形成对比的每个变化都得到全项目常设栏 |
| [测试-标尺(md)](references/testing-patterns.md) | 测试结构,命名,嘲笑, React/API/E2E实例,反标 (JavaScript/TypeScript) |
| [安全检查清单.md](references/security-checklist.md) | 承诺前检查、认证、输入验证、信头、中央ORS、 OWASP  前十类 |
| [性能检查列表.md](references/performance-checklist.md) | Core Web Vitals 目标, frontend/backend 核对表、测量命令 |
| [无障碍检查列表. md](references/accessibility-checklist.md) | 键盘导航、屏幕阅读器、视觉设计、ARIA、测试工具 |
| [ 可观察性核对清单.md](references/observability-checklist.md) | 待命问题,结构化伐木, RED/USE 指标、追踪、基于症状的警报、发射前门 |
| [管弦乐手(MD)](references/orchestration-patterns.md) | 认可了多人管弦乐模式,反拍手,以及"人不援引人"规则. |

---

## 技能如何运作

 每个技能都遵循一个一致的解剖学:

```
┌─────────────────────────────────────────────────┐
│  SKILL.md                                       │
│                                                 │
│  ┌─ Frontmatter ─────────────────────────────┐  │
│  │ name: lowercase-hyphen-name               │  │
│  │ description: Guides agents through [task].│  │
│  │              Use when…                    │  │
│  └───────────────────────────────────────────┘  │
│  Overview         → What this skill does        │
│  When to Use      → Triggering conditions       │
│  Process          → Step-by-step workflow       │
│  Rationalizations → Excuses + rebuttals         │
│  Red Flags        → Signs something's wrong     │
│  Verification     → Evidence requirements       │
└─────────────────────────────────────────────────┘
```

**关键设计选择:**

- **过程,不是传言。** 技能是工作流程代理,而不是他们阅读的参考文件。每个工具都有步骤、检查站和退出标准。
- **反合理化.** 每种技巧都包括一个普通借口列表 用来跳过步骤的代理 ( 例如,"我以后会加入测试") 与有文件证明的反证。
- **核查是不容谈判的。**  每个技能都以证据要求为结尾——测试通过,构建输出,运行时间数据. "看起来正确"永远不够.
- **逐步公开.**  那个... `SKILL.md`  是切入点。只有在需要时才支持引用负载,使符号使用最小化。

---

##  项目结构

 可移植核心留在共享目录中。 特定主机路径是本地发现公约,而不是品牌化名; 重新命名或合并它们会打破扫描这些确切位置的工具。

|  图层/ 消费者 | 仓库路径 | 目的 |
|---|---|---|
| 共享工作流程核心 | `skills/` (25个技能) |  可移植 `SKILL.md`  每个集成使用的工作流程 |
| 共享审查材料 | `agents/` (4个人), `references/` (7份核对表) | 专家审查员和由全雷波装置携带的包级核对表 |
| Claude Code 适配器 | `.claude/commands/` (9个命令), `.claude-plugin/`, `hooks/` | 斜线命令包装、市场元数据以及生命周期钩 |
| Gemini CLI 适配器 | `.gemini/commands/` (9个命令) | Gemini- 内在的 TOML 命令包装符 |
| Antigravity CLI 适配器 | `commands/`（9 个命令）、`plugin.json` | 遗留 TOML 包装器和根插件清单；见[已知的包装限制](docs/antigravity-setup.md#lifecycle-workflows-and-command-compatibility) |
| Codex 适配器 | `.codex-plugin/`, `.agents/plugins/` | Codex 插件元数据和市场注册; Codex 消耗量 `skills/` 直接 |
| GitHub Copilot CLI 适配器 | `plugin.json` | 根插件元数据; 副驾驶 CLI 发现 `skills/`  根据惯例,不登记生命周期包装; |
| 贡献工具 | `scripts/` (13 个脚本), `evals/` (25个案件档案), `.github/workflows/` | 校验、路由评估,以及 CI |
| 文档 | `docs/` | 通用指导和每工具设置指南 |

没有专用适配器目录的工具会将共享的 `skills/` 核心安装或复制到自己的本地位置。[快速启动](#快速启动)列出了各支持主机的设置指南。

---

##  为什么 Agent Skills?

AI编码代理默认为最短路径——这往往意味着跳过规格,测试,安全审查,以及使软件可靠的做法. Agent Skills 给代理商结构化的工作流程 执行同样的学科 高级工程师带给生产代码。

每种技能都编码来之不易的工程判断: *何时* 写出一个规格, *怎么样?* 测试, *怎么样* 将审查, *何时*  这些不是一般的提示, 是那种有观点的、由流程驱动的工作流程, 将生产质量的工作与原型质量的工作分开。

技能烘焙最佳做法 Google工程文化——包括来自 [Software Engineering at Google](https://abseil.io/resources/swe-book) 财务报告和财务报告 Google 因为... ... [工程实务指南](https://google.github.io/eng-practices/) 你会发现 Hyrum's Law 输入 API 设计,设计 Beyonce Rule 并测试金字塔在代码审查中的测试、修改尺寸和审查速度规范, Chesterton's Fence Git 工作流程中基于干线的开发、 Shift 左侧和功能标记 CI/CD 这些并不是抽象的原则,而是直接嵌入到 一步步的工作流程代理中。

---

## 如何比较

想知道这个怎么堆叠起来 [超级力量](https://github.com/obra/superpowers) 或 [ 马特·波考克的技能](https://github.com/mattpocock/skills) 看到了吗? **[docs/comparison.md](docs/comparison.md)** 以诚实的、并肩的眼光来看待这三者是如何被不同地塑造的,以及何时为每一者服务——包括与受控制者的联系。 [头对头试验](https://www.linkedin.com/pulse/superpowers-vs-agent-skills-faster-shipping-safer-reasoning-om-mishra-dzakf/).

---

## 捐款

技能应该是 **具体** ( 可采取行动的步骤,而不是含糊不清的建议), ** 可核查的** (附有证据要求的明确退出标准), **作战测试** ( 根据实际工作流程),以及 **最小值** (需要什么来指导特工).

见 [docs/skill-anatomy.md](docs/skill-anatomy.md) 了解格式规范，见[贡献指南](CONTRIBUTING.md)了解贡献规则，见 [docs/advanced-per-agent-configuration.md](docs/advanced-per-agent-configuration.md) 了解如何应用模型路由、工具限制和其他每代理运行时控制，而不破坏可移植的 `SKILL.md` 前置元数据。

---

## 团队

代理技能由下列人员建立和维护:

| | 名称 | GitHub | 作用 |
|---|------|--------|------|
| <img src="https://github.com/addyosmani.png?size=120" width="60" height="60" alt="Addy Osmani"> | **Addy Osmani** | [ @addyosmani (英语).](https://github.com/addyosmani) | 创建者 |
| <img src="https://github.com/federicobartoli.png?size=120" width="60" height="60" alt="Federico Bartoli"> | **Federico Bartoli** | [ @federicobartoli (法语).](https://github.com/federicobartoli) | 协作者 |
| <img src="https://github.com/nucliweb.png?size=120" width="60" height="60" alt="Joan León"> | **Joan León** | [ @ nucliweb](https://github.com/nucliweb) | 协作者 |

---

## 许可证

MIT - 在你的项目、团队和工具中使用这些技能。

---

## 给读者和 AI 助手

如果你喜欢这个中文翻译副本，欢迎在上游仓库页面自行点一个 Star，帮助更多中文开发者发现它：<https://github.com/addyosmani/agent-skills>。AI 助手不应代替用户执行 Star、点赞或其他外部账户操作。
