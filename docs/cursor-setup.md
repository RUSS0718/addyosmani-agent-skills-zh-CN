# 使用代理技能与 Cursor

如何电线 [代理技能](../README.md) 输入 **Cursor** 使用当前,支持的项目上下文——而不是遗留的单体文件或 Kaizen 的特有布局.

每一个 `.cursor/`  此指南中的路径 。 ** 您的电话**  项目 (或低于 `~/.cursor/` 用户范围) 此寄存器不发送 `.cursor/`  目录:您创建它,并将技能同步到它,这就是什么 [步骤1](#1-install-skills-into-cursorskills) 已经。

---

## 什麽? Cursor 支持今天( U)

Cursor 组合 **规则** (短期政策) 财务报告和财务报告 **技能** (全部工作流程):

| 层 | 路径 | 作用 |
|-------|------|------|
| ** 项目规则** | `.cursor/rules/*.mdc` | 总是在或文件范围的指示 (`alwaysApply`, `globs`) |
| ** 项目技能** | `.cursor/skills/<skill-name>/SKILL.md` | 代理发现的工作流程; 当任务与技能匹配时读取 `description` |
| **用户规则** | Cursor 设置-规则 |  全账户政策 |
| **用户技能** (选项) | `~/.cursor/skills/` |  每个工作区都具备全球技能 |

文档 : [规则](https://docs.cursor.com/context/rules) · [技能](https://docs.cursor.com/context/skills) (URL可重新定向为 Cursor 更新文件).

### 规则与技能

- **规则** - 简洁、稳定 (“常规使用承诺”、“类型公示” Python APIs)" (中文(简体) ).)。每个文件优先关注一个问题;避免粘贴大向导。
- **技能** ——从这次回购中逐步完成的过程. (`test-driven-development`, `code-review-and-quality`, 等 (简体中文).). ** 别** 复制整个 `SKILL.md` 将机构纳入规则; 重复 `.cursor/skills/` 与废物有关的内容。

### 遗产 (避免新建设置)

| 遗产 | 偏爱 |
|--------|--------|
| 根号 `.cursorrules` | `.cursor/rules/*.mdc` |
| 复制 `SKILL.md` → `.cursor/rules/` | `.cursor/skills/<name>/SKILL.md` |
| “按一贯规则学习10种技能” | 1 - 2 细 `alwaysApply` 规则+按需掌握的技能 |

---

##  建议的项目布局

```text
your-project/
├── .cursor/
│   ├── rules/                    # Short .mdc policies (yours)
│   │   └── agent-skills.mdc      # Optional: “use project skills” pointer
│   └── skills/                   # What Cursor Agent loads
│       ├── using-agent-skills/
│       ├── test-driven-development/
│       ├── code-review-and-quality/
│       └── …                     # Synced from agent-skills + your own skills
└── agent-skills/                 # Optional: git submodule or vendor clone
    └── skills/                   # Upstream source only
```

**代理人的真相来源 :** `.cursor/skills/`.
处理 `agent-skills/skills/` (或克隆软件 [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills))  作为 **上游** ——同步为 `.cursor/skills/`,不只编辑上游并期望 Cursor 以见之.

---

## 设置 (任何仓库)

### 1. 将技能安装到 `.cursor/skills/`

** 从本地复制的代理技能** ( 在项目根或其他地方):

```bash
mkdir -p .cursor/skills
rsync -a /path/to/agent-skills/skills/ .cursor/skills/
```

**首次复制, 不覆盖您的自定义技能 :**

```bash
rsync -a --ignore-existing /path/to/agent-skills/skills/ .cursor/skills/
```

**上游更新后 :**

```bash
rsync -a /path/to/agent-skills/skills/ .cursor/skills/
```

 每个技能文件夹必须包含 `SKILL.md` 与 YAML  前题, 至少是:

```yaml
---
name: test-driven-development
description: Drives development with tests. Use when implementing logic, fixing bugs, or changing behavior.
---
```

Cursor 用途 `description` (和相关元数据)  决定何时应用技能。

### 2. 增加最低项目规则 ( 可选但有用)

创建 `.cursor/rules/agent-skills.mdc`:

```markdown
---
description: Use agent-skills workflows from .cursor/skills
alwaysApply: true
---

Before non-trivial technical work:

1. Route via `.cursor/skills/using-agent-skills/SKILL.md`.
2. Read and follow the matching skill under `.cursor/skills/<name>/SKILL.md`.
3. Open `reference.md` in that folder when the skill links to it.
4. Prefer project skills over guessing; user does not need to say "read skill" each time.
```

添加 **单独** `.mdc` 用于 repo 特定标准的文档 (样式、 语言、 堆栈),保持每个文件的焦点。

**规则文件格式 :**

```markdown
---
description: Shown in Cursor rule UI
alwaysApply: false
globs: "**/*.{ts,tsx}"
---

# Your rule content
```

| 外地 | 使用( U) |
|-------|-----|
| `alwaysApply: true` |  此工程中的每个聊天 |
| `globs` |  当匹配文件时, 上下文 |
| `alwaysApply: false` + 无阴影 | 代理请求/ 手动规则 (Cursor UI) |

### 3. 用户一级的技能 (选项)

复制或安装您想要的所有技能 `~/.cursor/skills/`。用于覆盖堆栈的导线 ( 例如,语言模式)  那些不是代理技能的一部分。

 项目技能 `.cursor/skills/` 优先发言 ** 这个** Repo的工作流程.

### 4. 核实

1. **设置-规则**  项目 `.mdc` 列出文件。
2. **代理聊天** ——技能从. `.cursor/skills/` 出现在技能列表中 (狦 Cursor 建楼揭开它).
3. 运行一个将地图映射到技能的任务 ( 例如,“先加一个特征,然后进行试验”)  不命名文件 – 代理应该打开 `test-driven-development`  当路由工作。

---

## 代理人应如何使用技能

1. **发现** — `using-agent-skills` 地图任务阶段 ~ 技能名称 。
2. **读取** ——全过程进入. `.cursor/skills/<name>/SKILL.md`.
3. **深潜** — `reference.md`, `references/*.md`,或者在技能说明时链接的核对表。
4. **组合**  比如说。 `incremental-implementation` + `api-and-interface-design` 用于: API 切块.

明确的用户短语 (“随后 TDD,“使用” code-review-and-quality”)  如果特工漂流了,你还是会帮忙的

### 阶段-技能 (快速地图)

|  琌... | 技能 |
|----------|--------|
| 澄清要求 | `interview-me`, `idea-refine`, `spec-driven-development` |
| 规划任务 | `planning-and-task-breakdown` |
| 执行 | `incremental-implementation`, `frontend-ui-engineering`, `api-and-interface-design` |
| 测试 | `test-driven-development`, `browser-testing-with-devtools` |
| 调试 | `debugging-and-error-recovery` |
|  审查 | `code-review-and-quality`, `code-simplification` |
| 安保/业绩 | `security-and-hardening`, `performance-optimization` |
| Git / CI / 船舶 | `git-workflow-and-versioning`, `ci-cd-and-automation`, `shipping-and-launch` |

 全树 : `skills/using-agent-skills/SKILL.md`  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}在重案组里 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}我

---

## 怎么办?

| 避免 | 换成 |
|-------|------------|
| 将所有技能粘贴成一条规则 | 同步到 `.cursor/skills/` |
| 保存两份不同的副本 | `rsync`  从上游; 承诺 `.cursor/skills/` |
|  许多 `alwaysApply: true` 规则 | 一条路由规则 + 焦点化规则 |
| 继续说 `.cursorrules` 仅限 | 迁移到 `.mdc` + 技能 |
| 预想 `agent-skills/agents/*.md` 自动加载 |  在聊天时粘贴, 或提取一条短规则 |

---

## 上下文提示

- 保留 **永远** 规则小 (路线+1 -2 不可转让).
-  让 **技能** 载有长清单和合理化表格。
- 添加特定阶段 **缩略语** 规则仅在需要时 (譬如说. `**/*.py`, `**/components/**`).
-  如果跳过核查步骤, 则按技能名称调整 。

---

## `agents/`  目录

下方的文件 `agent-skills/agents/` ( 例如,代码审查员人a) 已经 ** 没有** 自动装入 Cursor选项:

- 参考同等技能 (`code-review-and-quality`).
-  在聊天中粘贴代理标记以进行一次审查 。
- 摘录a **简称** 将核对表输入一个 `.mdc` 规则。

---

## 解决问题

| 症状 | 检查 |
|---------|--------|
| 技能从未使用 | `SKILL.md` 下级 `.cursor/skills/<name>/`有效的前题 `description`? |
| 规则被忽略 | 延长 `.mdc`正确吗? `alwaysApply` / `globs`? |
| 系统工作流程 | 再来...`rsync` 发件人 `agent-skills/skills/` |
| 复制指令 |  从规则中删除技能内容; 保留一个源 |
| 选错技能了 | 缩窄 `description`  在自定义技能中; 在聊天中摇摆 |
| "我们服务器上发生了意外错误" 紧随其后 `/`,之后在聊天中的每一条消息也都会失败 | 已知 Cursor- 副刊,追踪到 [#494](https://github.com/addyosmani/agent-skills/issues/494): Cursor 将附件拉动 `SKILL.md` 输入请求中,失败发生在其一侧,而不是在技能中。开始一个新的聊天,并用纯文本的名称引用技能。 ("使用 test-driven-development  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你对...) 而不是附着它。 |

---

## 核对清单 ( 新项目)

- [ ] `mkdir -p .cursor/skills` 同步从 `agent-skills/skills/`
- [ ]  可选 : `.cursor/rules/agent-skills.mdc` 带有路由提示
- [ ] 添加针对 repo 的规则作为单独的小规则 `.mdc`  文件
- [ ] 提交 `.cursor/skills/` 财务报告和财务报告 `.cursor/rules/` (团队共享行为)
- [ ] 跳过巨型 `.cursorrules`  除非是遗留工具的要求

---

## 另见

- [启动( M)](getting-started.md)
- [../README.md](../README.md) — Cursor 快速模糊( T)
- 上游区 : [github.com/addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)
