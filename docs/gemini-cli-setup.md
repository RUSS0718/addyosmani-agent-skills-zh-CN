# 使用代理技能与 Gemini CLI

## 设置

### 备选方案1:作为技能安装 ( 建议)

Gemini CLI 拥有自动发现的本土技能系统 `SKILL.md`  文件在其中 `.gemini/skills/` 或 `.agents/skills/`  目录。 每种技能在匹配您的任务时都会按要求激活 。

** 从 repo 安装 :**

```bash
gemini skills install https://github.com/addyosmani/agent-skills.git --path skills
```

** 或者从本地克隆安装 :**

```bash
git clone https://github.com/addyosmani/agent-skills.git
gemini skills install /path/to/agent-skills/skills/
```

**仅为特定工作空间安装 :**

```bash
gemini skills install /path/to/agent-skills/skills/ --scope workspace
```

 在工作空间范围安装的技能进入 `.gemini/skills/` (或 `.agents/skills/`)。用户一级的技能进入 `~/.gemini/skills/`.

安装后,用:

```
/skills list
```

Gemini CLI  在提示中自动输入技能名称和描述。当它识别匹配任务时,它会请求允许在装入全部指令之前激活该技能。

### 备选案文2:性别、性别、性别、语言、语言、语言、语言、语言、语言、语言、语言、语言、语言、语言、语言等 (持久性背景)

 对于技能,您希望总是作为持久工程背景加载 (而不是点播激活),添加到您的项目中 `GEMINI.md`:

```bash
# Create GEMINI.md with core skills as persistent context
cat /path/to/agent-skills/skills/incremental-implementation/SKILL.md > GEMINI.md
echo -e "\n---\n" >> GEMINI.md
cat /path/to/agent-skills/skills/code-review-and-quality/SKILL.md >> GEMINI.md
```

 您也可以通过从单独文件导入模块化 :

```markdown
# Project Instructions

@skills/test-driven-development/SKILL.md
@skills/incremental-implementation/SKILL.md
```

使用( U) `/memory show` 校验装入的上下文,以及 `/memory reload` 更改后刷新。

> **技能对GEMINI.md:** 技能是需求上的专门知识,只有在相关时才会激活,保持上下文窗口的清洁. GEMINI.md为每次快速输入提供持续上下文. 将技能用于特定阶段的工作流程,将GEMINI.md用于项目常规。

##  建议配置

### 总是打开 (GEMINI.MD 加纳)

添加这些内容,作为每届会议的持久背景:

- `incremental-implementation` ——用可核查的小片块建造.
- `code-review-and-quality` ——五轴审查.

### 待命状态 (技能)

将这些设备安装为技能, 只在相关时激活 :

- `test-driven-development` —— 在执行逻辑或修复错误时激活
- `spec-driven-development` ——启动新项目或新功能时激活.
- `frontend-ui-engineering` ——建设时激活. UI
- `security-and-hardening` ——安全审查期间启用.
- `performance-optimization` - 在业绩工作期间开展活动

## 高级配置

### MCP 一体化

 这个包里有很多技能 [示范背景协议 (MCP)](https://modelcontextprotocol.io/) 用于与环境互动的工具。例如:

- `browser-testing-with-devtools` 使用该 `chrome-devtools` MCP 扩展名。
- `performance-optimization`  可受益于与业绩有关的 MCP 工具。

 为了实现这些目的,确保您拥有相关的 MCP 安装在您的扩展 Gemini CLI 配置 (`~/.gemini/config.json`).

###  会话钩

Gemini CLI 支持会话寿命周期钩子。您可以在会话开始时使用这些钩子自动注入上下文或运行验证脚本。

复制 `agent-skills`  从其他工具获得的经验,可以配置一个 `SessionStart` 钩子可以提醒你拥有的技能 或者装入一个元技能。 `hooks/session-start.sh`  在这个repo中是一个现成的脚本,用于: 它打印 `using-agent-skills` 元技能作为标准 `SessionStart` JSON 信封在 Stdout 上。

### 明确上下文加载

 您可以通过引用当前会话中的任何技能来明确加载该会话 `@` 符号在您的提示中 :

```markdown
Use the @skills/test-driven-development/SKILL.md skill to implement this fix.
```

 当您想要确保不等待自动发现而遵循特定的工作流程时,此功能是有用的 。

## 斜线命令

退舰9号攻击命令下方 `.gemini/commands/`: 8 个生命周期命令加 `/webperf` 专家审计。 Gemini CLI  从项目根运行时自动发现它们。

| 命令 |  它会做什么? |
|---------|--------------|
| `/spec` |  在写入代码前写一个结构化的光谱 |
| `/constraints` | 定义并强制执行项目的质量栏 |
| `/planning` | 将工作分成可核查的小任务 |
| `/build` | 逐步执行下一个任务 |
| `/test` | 运行 TDD 工作流程——红色、绿色、重构 |
| `/review` |  五轴码检讨 |
| `/code-simplify` |  在不改变行为的情况下降低复杂性 |
| `/ship` |  通过平行人员发射前核对表a |
| `/webperf` | 用于审计浏览器的软件 Core Web Vitals 执行情况和业绩问题 |

 每个命令自动引用相应的技能——不需要手工技能加载.

> **说明:** 使用( U) `/planning` 改为 `/plan` — `/plan` 与 a 冲突 Gemini CLI 内部命令名称.

##  用法提示

1. ** 比GEMINI.md更偏爱技能** ——技能在需求时激活,并保持上下文窗口的焦点。只有在您需要时,才能将技能输入GEMINI.md中。
2. **技能说明事项** - 每一个SKILLL.MD有一个 `description` 字段,以告诉代理何时激活它。此重播中的描述被优化,以便自动发现所有支持的工具。 (Claude Code, Gemini CLI, 等 (简体中文).) 清楚说明两者 *怎么样?* 技术有,而且 *何时*  它应该触发。
3. **使用代理审查** - 复制 `agents/code-reviewer.md` 请求结构化代码审查时的内容。
4. **与参考资料合并** - 参考清单来自 `references/`  在测试或性能等特定质量领域工作时。
