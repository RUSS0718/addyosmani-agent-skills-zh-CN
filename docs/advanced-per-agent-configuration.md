# 高级每代理配置

 该存储库中的技能是有意可移植的。 `SKILL.md` 将其前题限制在 [Agent Skills 规格](https://agentskills.io) 定义,所以同一文件可以跨 Claude Code, Cursor, Gemini CLI反重力 还有任何其他符合规格的客户端

多个代理也支持额外的运行时间控制: 模式路由、 工具限制、 转弯限制、 执行隔离 。 这些控制是有价值的, 但它们是针对供应商的, 不属于共享的、 可移植的前题 。 本文解释了每个代理支持的控件, 属于何处, 以及如何在不突破可移植性的情况下将其应用为选入 。

 这是每个代理配置的条理参考,它取代了先前将供应商字段添加到顶层的做法 `SKILL.md`  前题 。 `kind`, `model`, `temperature`, `max_turns`, `tools`,以及 `context` 不得在高级技能中出现。 `metadata` 或置于单独的每剂适配器文件中,如下文所述.

## 原则

1. **保留 `SKILL.md` 便携。**  那个... Agent Skills 规格保留顶级前题 `name`, `description`, `license`, `compatibility`, `metadata`,和实验 `allowed-tools`. 供应商或客户特定财产属于 `metadata`,而不是在顶层。
2. **未知的顶级字段不能被忽略 。**  许多解析器只读 `name` 财务报告和财务报告 `description` 并默不作声地放下其余的,但严格规格的验证人或客户端可能会标出或拒绝未识别的顶级密钥。 `metadata` 将文件保存在任何地方。
3. **Opt-in,不是环球的.** 运行时间控制应该是一个用户为自己的代理和工作流程添加的东西,而不是默认烘焙到存储器的每一种技能中.
4. **更爱生成于手编.**  如果一个配置是重复的和机械的,则从一个单一的真理源生成它,而不是手工保存副本。见 [使用脚本自动化](#automating-with-scripts).

## 供货区所属地区

| 字段类型 | 示例 | 正确家门 |
|---|---|---|
| 光谱字段 | `name`, `description`, `license`, `compatibility` | 顶级前题在 `SKILL.md` |
| 供应商元数据 | 模式提示、 路由标记 | 下层 `metadata` 键入 `SKILL.md` |
| 运行时协奏曲 | 子代理定义、 转弯限制、 工具允许列表 | 单个代理适配器文件 ( 见下面的每个代理) |

## Claude Code

Claude Code 读取 `name` 财务报告和财务报告 `description`  自定义命令和技能共享相同的前题,所以 `context` 财务报告和财务报告 `allowed-tools`  二、两个领域下的工作 `.claude/commands/*.md` 财务报告和财务报告 `.claude/skills/*/SKILL.md`.

- **`context: fork`**  在孤立的子代理语境下运行命令或技能,而不是主动对话。这可以使上下文处于孤立状态,从干净状态中重复执行,执行符号花在叉子而非主会话中,以及更清洁的交接,只显示结果返回。 **`agent`** 字段选择哪个子代理类型运行叉 (举例来说, `agent: Plan`); 它默认为 `general-purpose`.
- **`allowed-tools`** 预批准工具; 它不限制它们。 列表中的工具运行时, 没有引用命令或技能的转弯时的许可提示, 并在您的下一个信件上清除了授予。 工具池中没有任何东西被删除 。
- **`disallowed-tools`**  是实际限制的字段 : 它会在技能活动时从可用池中移除列出的工具, 而这是审查或审计等被动阶段的最低优先级需要的。 此限制也会在您的下一个信件中清除 。 注意 Claude Code 字段,而非规格之一的 6,所以属于本地副本或适配文件; 属于已出版的 `SKILL.md`  它以出乎意料的密钥错误失败了包装。

 这些是 Claude Code 保持分享、公布 `SKILL.md` 限于 spec 字段; 将这些运行时控件应用到本地命令或技能副本中。

## Gemini CLI 和反重力

Gemini CLI 和反重力一样发现技能:它们匹配 `name` 财务报告和财务报告 `description`,然后按要求装入全部技能。运行时的管弦乐生活在单独的资源中,而不是技能的前题。

- **亚剂定义** 住在这里 `.gemini/agents/*.md`,这与技能不同。 `kind` (`local` 或 `remote`), `model`, `temperature`, `max_turns`,以及 `tools`。请注意,这是一个副剂计划,而不是一个技能计划:不应将两者合并成一个 `SKILL.md`.
- **模式路线**  可以发送较轻的任务 (格式化、文档、 git 操作) 以更快的模型,为认知要求高的工作保留更强的模型 (调试、安全审计、接口设计).
- **工具限制**  每个子代理执行最小优先级,它也会从提示中修剪未使用的工具计划.
- **转弯限制** (`max_turns`) cap 执行以避免运行的校正循环 。

### 示范指南

Google  建议省略 `temperature` (单位:千美元) Gemini 3.x 模型和使用 `thinking_level`  相反。不要硬代码 `temperature` 以3.x型号排列; 偏好 `thinking_level` 并随着模型的发展重新审视这一指导。

```yaml
# .gemini/agents/security-auditor.md
---
kind: local
model: gemini-3-pro
thinking_level: high   # not: temperature: 0.1
tools: [read_file, grep]
max_turns: 10
---
```

## 使用脚本自动化

 通过手动保持每个技术的每个代理元数据是重复的,不同步的,很容易出错。 更好的方法是保持一个单一的真理源并生成特定代理输出:

- 生成器读取每个技能配置图 (每种技能应该用于给定的代理).
-  对于元数据类型字段,它会在其下方注入 `metadata`  每个键 `SKILL.md`保持文件的规格一致。
-  对于管弦风格的字段,它会发出一个代理机所期望的单独的适配器文件,例如: `.gemini/agents/*.md` 子剂定义,离开 `SKILL.md` 毫无动静。

生成还允许工具验证 **语义学,不只是形状**:确认模型名称是已知的模型,工具名称是真实的工具, `max_turns`  是正整数,而且 `temperature` (仍在适用的情况下)  在区域中是有效的数字。只有形状检查允许无效的值,例如 `parseInt("1.5")` 或 `parseFloat("0.2oops")` 滑过。

## 状态

本文合并了以下讨论内容: [#272](https://github.com/addyosmani/agent-skills/pull/272), [#36](https://github.com/addyosmani/agent-skills/pull/36),以及 [#35](https://github.com/addyosmani/agent-skills/issues/35),所有都关闭了此方法。这是每个代理配置的单一参考:它保留共享技能的便携式,同时将每个代理提供的高级控制记录为选入。上面描述的基于脚本的自动化是在执行前有待达成协议的建议。
