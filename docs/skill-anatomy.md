# 技术解剖学

 此文档描述了代理技能文件的结构和格式。 当贡献新技能或了解现有技能时, 将此作为指南 。

##  文件位置

 每个技能都生活在自己的目录之下 `skills/`:

```
skills/
  skill-name/
    SKILL.md           # Required: The skill definition
    scripts/           # Optional: Runnable helpers used by the skill workflow
    references/        # Optional: Skill-specific reference documentation
    supporting-file.md # Optional: Reference material loaded on demand
```

`SKILL.md`  是唯一需要的文件。添加 `scripts/` 或 `references/` 只有在技能真正需要它们时,才完全省略它们,以便更简单的技能.

## SKILL.md 格式

###  一. 前题 (要求数)

```yaml
---
name: skill-name-with-hyphens
description: Guides agents through [task/workflow]. Use when [specific trigger conditions].
---
```

**规则:**
- `name`: 小写,连字符分隔。必须匹配目录名称 。
- `description`:从技能在第三人称中的表现开始,然后包括一个或多个清晰的“使用时”触发条件。同时包含两个条件。 *怎么样?* 财务报告和财务报告 *何时*。最多1024个字符。
- 顶级密钥仅限于 [Agent Skills 规格](https://agentskills.io) 字段 : `name`, `description`, `license`, `compatibility`, `metadata`,以及 `allowed-tools`。供应商或运行时间控制,例如 `model`, `tools`, `max_turns`,或 `context` 下潜 `metadata` 或每个代理适配器文件;见 [docs/advanced-per-agent-configuration.md](advanced-per-agent-configuration.md)。验证器拒绝任何其他顶级密钥。

已公布的名称是兼容性标识符。
`browser-testing-with-devtools`  是稳定的上游名称,因为其他
下游目录可能重新命名它,但
 目录拥有别名或迁移映射;仅下游别名不是
保存在本仓库中。

** 为什么这很重要:** 代理通过读取描述来发现技能. 描述被注入到系统中的即时,所以它必须同时告诉代理提供的技能以及何时激活它. 不要概括工作流程——如果描述包含过程步骤,代理可以遵循摘要而不是读取全部技能.

### 标准段 ( 建议的模式)

需要上面的前题合同。下面的区块布局是建议的模式,而不是僵硬的模板:当等同的标题明确服务于同一目的时,是可以接受的。

```markdown
# Skill Title

## Overview
One-two sentences explaining what this skill does and why it matters.

## When to Use
- Bullet list of triggering conditions (symptoms, task types)
- When NOT to use (exclusions)

## [Core Process / The Workflow / Steps]
The main workflow, broken into numbered steps or phases.
Include code examples where they help.
Use flowcharts (ASCII) where decision points exist.

## [Specific Techniques / Patterns]
Detailed guidance for specific scenarios.
Code examples, templates, configuration.

## Common Rationalizations
| Rationalization | Reality |
|---|---|
| Excuse agents use to skip steps | Why the excuse is wrong |

## Red Flags
- Behavioral patterns indicating the skill is being violated
- Things to watch for during review

## Verification
After completing the skill's process, confirm:
- [ ] Checklist of exit criteria
- [ ] Evidence requirements
```

## 节目的

### 概览
技能的"电梯投球",应该回答:这种技能有什么作用,为什么一个特工要跟着它呢?

### 何时使用
帮助代理和人类决定此技能是否适用于当前任务。 包含两个正触发器 ("使用当X") 和否定性排除 ("没有Y").

### 核心进程
技能的核心:这是代理人所遵循的分步工作流程,必须是具体和可操作的,而不是模糊的建议。

**不错:** 运行( Run) `npm test` 并验证所有测试合格证"
**错误 :** "确保测试有效"

### 共同合理化
 这些是代理商用来跳过重要步骤的借口,并配以反驳,它们阻止代理商在跟踪过程中实现合理化。

想想每次有经纪人说"我以后会加入测试"或"这足够简单,可以跳过光谱"——那些带着一个事实性的反证到这里.

### 红旗
 可观测到技能遭到侵犯的迹象。在代码审查和自我监测期间很有用。

### 核查
退出标准。 代理用于确认技能过程的核对表已经完成。 每个复选框都应该有证据可核查 。 (测试输出、构建结果、截图等。).

## 辅助文件

仅在以下情况下创建辅助文件:
- 参考材料超过100行 (保持主 SKILL.md 的焦点)
- 需要代码工具或脚本
- 核对清单足够长, 足以证明单独文件的合理性

 在50行以下时保持规律和原则的内在化.

 如果技能不需要可运行的助手, 请不要创建空的 `scripts/`  目录只是为了映射其他技能。空目录会添加噪音而不会改变技能的运作方式。

## 共享参考文献

不止一种技能使用的核对表——测试、安全、性能、无障碍性、定义——生活在 `references/`  在寄存器根处, 故意 * 没有*  在任何技能目录中。

 这是一个包级的设计选择。 Agent Skills Spec将技能描述为自成一体的目录, 但这里有几种技能指向相同的核对表。 将这些核对表组合起来将强制要求两个选项之一: 将核对表复制到每个使用它的技能中, 或选择一种技能“ 拥有” 并让其他技能到达该目录中。 这两种技能都随时间推移而变化。 单一的重写本将保留真理的来源 。

权衡是可移植性:一个全repo安装 ( 例如, Claude Code 市场插件) 携带 `references/` 随身携带,但每个技能安装 仅复制 `skills/<name>/` 留下重根的兄妹,这些联系一无所获。 [#361](https://github.com/addyosmani/agent-skills/issues/361).

现行公约:完全由一种技能使用的材料是该技能目录中的辅助文件;交流各种技能的材料进入 `references/`.

## 环境效率

技能负载按要求: 只有技能名称和描述在启动时处于上下文中。 完整 `SKILL.md` 仅在代理商决定相关技能时才加载。 要保持负载便宜:

- **保留 `SKILL.md` 五百行内.** 将详细的参考材料移动到辅助文件。
- **写具体描述.** 精确的描述可以帮助代理在正确的时刻激活技能,否则就跳过.
- **采用渐进披露.** 仅当工作流程到达时才读取的引用辅助文件.
- **更喜欢脚本 超过内线代码。** 执行一个脚本不会消耗上下文;只有它的输出才会消耗上下文。每个负载都会支付内置代码块的费用。
- **将文件参考文献保留在一个深层 。** 直接链接从 `SKILL.md` 用于支持文件,而不是通过中间文档链条。

## 脚本要求

 当有技能的船舶在船下航行时, `scripts/`,每个文字都遵循这些公约:

- 使用一个 `#!/bin/bash`  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}谢邦
- 使用( U) `set -e` 以失败的快速行为。
- 将状态消息写入 stderr : `echo "Message" >&2`.
- 写入机器可读输出 (JSON) 来打个招呼。
- 包含一个临时文件的清理陷阱 。
- 引用脚本路径为 `skills/<skill-name>/scripts/<script>.sh` (相对关系).

## 写作原则

1. **过程超越知识。** 技能是工作流程,不是参考文献。步骤,不是事实。
2. ** {\fn黑体\fs20\shad2\2aH82\3aH20\4aH33\fscx95\3cH592001\be1}具体地说 {\fn黑体\fs20\shad2\2aH82\3aH20\4aH33\fscx95\3cH592001\be1}总的** 运行( Run) `npm test`"胜过"验证测试".
3. **证据胜于假设。**  每个核查复选框都需要证明.
4. **反合理化.**  每个值得跳跃的步骤都需要在合理化表中提出反驳。
5. **逐步公开.**  主 SKILL.md 是切入点。 支持文件只在需要时才加载 。
6. **触觉觉.**  每一节必须说明列入的理由。如果删除它不会改变代理行为,则删除它。
7. **型号中立.** 写程序,而不是工作。 [写程序,而不是工作](#write-the-procedure-not-the-workaround).

## 写程序,而不是工作

 这个组合中的技能运行在许多代理和模型世代上。因为一个模型得到一个特定的调用错误而存在的一步是对其他模型的一种责任:它制约了本可以直接解决任务的代理,而它花费的则是更强大的模型对于实际工作需要的转换。跨模型转移测量。 ([维基语录链接:名人名言 维基语录链接:名人名言 - 文学作品 - 谚语 - 谚语 - 谚语 - 谚语](https://arxiv.org/abs/2608.27454)) 显示针对一个模型执行失败而撰写的技能可以留下一个更强大的模型 * 比没有技能更糟糕*.

**规则:**  如果一个步骤不能不命名一个模型、一个模型版本或一个代理的私人工具名称来说明其合理性,则它属于一个问题,而不是技能。请描述该能力。 ("运行焦点测试命令","写入文件"),不是机制 一次运行碰巧暴露 (`run_command`, `write_to_file`).

 在提出技能内容之前,有两个气味需要检查:

- **超定力学.** 指定一个精确的指令表或序列化细节, 仅说明目标会做什么 。 如果理由是“ model X 得到这个错误 ” , 则它是一个绕行, 而不是一个程序 。
- **断裂诊断.**  一个多步骤的检查程序,一个步骤可以证实同样的事情。 每个额外的步骤都转弯,转弯预算是有限的。

 这是关于技巧的 *内容*; 可移动性 `references/` *路径*  是另一个问题 ( 见 [共享参考文献](#shared-references)).

## 命名公约

- 技能目录 : `lowercase-hyphen-separated`
- 技能文件 : `SKILL.md` (总是大写)
- 支持文件 : `lowercase-hyphen-separated.md`
- 共享引用: 存储在根中 `references/`  目录, 不在技能目录内 ( 见 [共享参考文献](#shared-references)  为什么).
- 技能特定参考文献: 单个支持的 Doc 可以在技能目录中作为松散文件保留 。 (编号 `Supporting files` 以上条目) 当一些相关文件以技能旅行时,新出现的自足、可分配技能的公约是将它们分组为一组。 `references/` 技能目录内部的目录,因此该技能带有自己的辅助文件。

## 交叉技能参考资料

 按姓名参考其他技能:

```markdown
Follow the `test-driven-development` skill for writing tests.
If the build breaks, use the `debugging-and-error-recovery` skill.
```

 不要在技能之间重复内容——参考和链接代替.

## 要求 vs 建议

要求 :

- A `skills/<skill-name>/SKILL.md`  文件
- 有效 YAML  前题 `name` 财务报告和财务报告 `description`
- 包括技能所作和何时使用在内的描述

 建议:

- 上面显示的标准区段流量
- 等同标题,例如 `How It Works`, `Core Process`,或 `Workflow`  当他们更自然地阅读技能的时候,
- 只有当文件保存主文件时才予以支持 `SKILL.md` 重点
