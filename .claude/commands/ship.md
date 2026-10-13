---
description: Run the pre-launch checklist via parallel fan-out to specialist personas, then synthesize a go/no-go decision
---

调用代理技能:shipping-and-launch 技术。

`/ship` 是一个 **风扇乐团**。它同时运行三个专家,以对抗当前的变化,然后将其报告合并为单一报告。 go/no-go 个人独立运作——没有共享状态,没有命令——这是使平行执行在这里安全而有用的。

## A阶段——平行粉丝退出

3个副剂同时使用代理工具。 **三个代理工具都用一个助手的转弯来调用, 以便同时执行** ——顺序调用使这个命令的目的落空.

内 Claude Code,每张通话证 `subagent_type` 匹配个人的a `name` 字段 :

1. **`code-reviewer`** ——运行五轴审查. (正确性、可读性、结构、安全性、性能) 。输出标准审查模板。
2. **`security-auditor`** 运行一个弱点和威胁模式。 OWASP  前10名,秘密处理, auth/authz提供标准审计报告。
3. **`test-engineer`** 分析变化的测试覆盖面。 找出幸福路径、边缘案例、错误路径和货币情景中的缺口。 输出标准覆盖面分析 。

 在其他没有Agent工具的拉带中,按顺序迅速引用每个人物的系统,并视其输出为平行返回——合并阶段仍然有效.

制约因素 (发件人 Claude Code子剂型号):
- 副剂不能产生其他副剂——不要让一个人向另一个人派遣代表。
-  每个子代理获得自己的上下文窗口,只返回它向这个主会话的报告.
-  如果你需要的队友 彼此交谈 而不是仅仅报告,使用 Claude Code 特务团队 将这些人称为队友类型 ( 见 `references/orchestration-patterns.md`).

**个人决议。**  如果你已经定义了自己的 `code-reviewer`, `security-auditor`,或 `test-engineer` 输入 `.claude/agents/` 或 `~/.claude/agents/`,这些优先于这个插件的版本—— `/ship` 自动拾取您的定制。 这是有意的: 插件子剂坐落在底部 Claude Code'范围优先表,因此用户级定义通过设计赢得.

## B阶段——主要背景下的合并

 一旦三个报告都回来了,主要特工 (不是一个子人a) 合成它们:

1. **代码质量** - 合计数 Critical/Important 调查结果 `code-reviewer` 和任何失败的测试、擦拭或构建输出。在审查者之间解决重复问题。
2. **警卫** - 促进任何 Critical/High `security-auditor` 与发射阻塞器的对照 `code-reviewer`安全轴
3. **业绩** - 从这里拉 `code-reviewer`'性能轴; 交叉检查 Core Web Vitals  在适用的情况下。
4. **无障碍** ——验证键盘导航,屏幕读取器支持,对比 (三人不涵盖——直接在这里处理,或援引无障碍核对表).
5. **基础设施** ——Env vars,迁移,监测,特征旗.
6. **文档** —— 读取, ADRs, 更改日志。 直接校验 。

## C阶段——决定和后退

产生单一产出:

```markdown
## Ship Decision: GO | NO-GO

### Blockers (must fix before ship)
- [Source persona: Critical finding + file:line]

### Recommended fixes (should fix before ship)
- [Source persona: Important finding + file:line]

### Acknowledged risks (shipping anyway)
- [Risk + mitigation]

### Rollback plan
- Trigger conditions: [what signals would prompt rollback]
- Rollback procedure: [exact steps]
- Recovery time objective: [target]

### Specialist reports (full)
- [code-reviewer report]
- [security-auditor report]
- [test-engineer report]
```

## 规则

1. 三个阶段A阶段的人是平行运行的——从来不是顺序运行的。
2. Personas不互相调用,主代理在B阶段合并.
3.  在政府作出任何决定之前,收回计划是强制性的。
4.  如果任何人a 返回临界结果,默认判决是NO-GO,除非用户明确接受风险。
5. **仅在以下所有情况属实时才跳过扇形 :** 更改触及到2个文件或更少,diff在50行以下,它不会触动认证、付款、数据访问,或者 config/env.  否则,默认为粉丝出局。 `/ship` 用于生产限制的变化——当爆炸半径为非三角时,即使diff看起来很小,也要进行平行审查。
