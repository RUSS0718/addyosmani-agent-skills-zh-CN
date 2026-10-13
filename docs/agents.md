# 代理人物

以单一视角发挥单一作用的专家人物。 Markdown  文件被您的调用所消耗为系统 (Claude Code, Cursor,副驾驶等.).

| 个人a | 作用 | 最佳办法 |
|---------|------|----------|
| [代码审查器](../agents/code-reviewer.md) | 高级工作人员工程师 | 合并前的五轴审查 |
| [安全审计师](../agents/security-auditor.md) | 安保工程师 | 脆弱性检测, OWASP- 类审计 |
| [测试工程师](../agents/test-engineer.md) | QA 工程师 | 试验战略、覆盖面分析、证明-It模式 |
| [网络业绩审计员](../agents/web-performance-auditor.md) | 网络性能工程师 | Core Web Vitals 审计, loading/rendering/network 分析 |

## 个人与技能和命令的关系

分三层,每层都有一份独特的工作:

| 层 | 或 | 示例 | 组成作用 |
|-------|-----------|---------|------------------|
| **技能** | 附有步骤和退出标准的工作流程 | `code-review-and-quality` |  那个... *怎么样* - 从人或命令内部援引 |
| **个人a** | 带有视角和输出格式的角色 | `code-reviewer` |  那个... * 谁* - 采取观点,提出报告 |
| **命令** |  一个面向用户的入口 | `/review`, `/ship` |  那个... *何时* ——由人物和技能组成. |

用户 (或斜线命令)  是管弦乐的 **人不叫别人。** 技能是个人工作流程中必修的跳跃.

## 何时使用

### 直接人a
 如果您想要对当前变化有一个视角, 且用户处于循环状态, 请选中此选项 。

- "审查这份公关" ——援引 `code-reviewer` 直接
- 是否有安全问题在 `auth.ts`~ 援引~ `security-auditor` 直接
- 校对:Soup `test-engineer` 直接
- "审计 Core Web Vitals  在产品页上” ——引用 `web-performance-auditor` 直接

### 斜线命令 (后面的单身汉)
 在有可重复的工作流程时选择这个 否则你每次都会重新解释

- `/review`  * 包装 `code-reviewer` 具备项目审查技能
- `/test`  * 包装 `test-engineer` 与 TDD 技能
- `/webperf`  * 包装 `web-performance-auditor` 网上应用软件中注重业绩的审计

### 斜线命令 (管弦乐家——粉丝出道)
单挑这个的时候 **独立** 调查可以平行进行,并产生单一代理人然后合并的报告。

- `/ship` 球迷们出去 `code-reviewer` + `security-auditor` + `test-engineer` ,然后将其报告综合成 go/no-go  决定

 这是Repo认可的唯一一种管弦图案。 [references/orchestration-patterns.md](../references/orchestration-patterns.md) 用于完整的图案目录和反标本。

##  决定汇总表

```
Is the work a single perspective on a single artifact?
├── Yes → Direct persona invocation
└── No  → Are the sub-tasks independent (no shared mutable state, no ordering)?
         ├── Yes → Slash command with parallel fan-out (e.g. /ship)
         └── No  → Sequential slash commands run by the user (/spec → /plan → /build → /test → /review)
```

## 工作实例:有效的协调安排

`/ship`  是这首歌中 虚构的粉丝乐团:

```
/ship
  ├── (parallel) code-reviewer    → review report
  ├── (parallel) security-auditor → audit report
  └── (parallel) test-engineer    → coverage report
                  ↓
        merge phase (main agent)
                  ↓
        go/no-go decision + rollback plan
```

为什么会这样:
-  每个副剂都在同一diff上运行,但生成一个 **不同视角**
-  他们彼此之间没有依存关系 真正的平行主义 真正的时钟储蓄
- 每一个运行在新上下文窗口中 ~ 主会话保持不乱
- 合并步骤是小的,并且从整个上下文中获益,所以它停留在主要代理人身上

## 工作实例:无效的管弦乐 ( 不构建此选项)

A `meta-orchestrator` 其工作是“决定他人应呼叫的”:

```
/work-on-pr → meta-orchestrator
                  ↓ (decides "this needs a review")
              code-reviewer
                  ↓ (returns)
              meta-orchestrator (paraphrases result)
                  ↓
              user
```

为何失败 :
-  无域值的纯路由层
- 增加两个解释性跳动 + 信息损失 + 2x 象征性费用
- 用户已经知道他们想要审查;让他们打电话 `/review` 直接
- 复制斜线命令和 `AGENTS.md` 意图映射已经做了

## 人的规则

1. Persona 是一个具有单一输出格式的单一角色。如果发现自己添加了第二个角色,则创建第二个人物。
2. **个人不援引他人。** 构成是斜线命令或用户的任务。 On Claude Code  这也是一个艰难的平台制约—— *"增殖剂不能产下其他亚剂"* -所以规则是为你而执行的
3. 一个人可以使用技能 (编号 *怎么样*).
4.  每个个人文件最后都有一个"组合"块,说明它适合哪里.

## Claude Code 间断

人在这个repo 设计的工作是 Claude Code 副特工,作为特工团队的队友,不作修改:

- ** 作为子剂 :** 启用此插件时自动发现 (不需要路径配置)中的代理工具。 `subagent_type: code-reviewer` (或 `security-auditor`, `test-engineer`). `/ship` 是一个典型的例子。
- ** 作为特工团队的队友** (实验, 需要 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`): 产下队友时引用同一个人的名字。 **附录** 队友的系统作为附加指令提示 ( 非替换),所以你的人 一个文本坐在顶端 团队协调指令 领先安装 (发送Message,任务列表工具等.).

子代理只向主代理报告结果。 Agent Teams 允许队友直接互换消息 。 在报告足够时使用子代理; 在 Agent Teams 需要互相质疑对方发现时使用 Agent Teams ( 例如,相竞的假说调试)。见 [references/orchestration-patterns.md](../references/orchestration-patterns.md) 用来绘制完整的地图

插件代理不支持 `hooks`, `mcpServers`,或 `permissionMode`  前题 – 这些字段被默默忽略 。 当这里写出新人物时, 避免依赖它们 。

## 添加新人a

1. 创建 `agents/<role>.md` 使用与现有人物相同的前题格式。
2. 定义角色、范围、输出格式和规则。
3. 添加一个 **组成** 下方块 ( 当 / 通过 / 不从他人 援引 时 , 直接 启动).
4.  在此文件顶端的表格中添加人称 。
5.  如果人物能产生新的管弦乐图案,请将其记录在 `references/orchestration-patterns.md` 而不是在个人文件中发明模式。
