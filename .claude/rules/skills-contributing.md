---
description: Anti-duplication guardrail for adding or changing skills
paths:
  - "skills/**"
---

# 添加或改变技能

 这个重播已经涵盖了大多数开发生命周期,所以大多数新技能的想法都与现有的技能或开放的PR重叠。 `skills/<name>/`  目录或对现有目录进行重大修改:

- 进行飞行前检查 [捐款额(美元)](../../CONTRIBUTING.md#before-proposing-a-new-skill): 搜索目录, 检查打开的 PR (`gh pr list --state open`),并证明差距是合理的。
- 优先扩展已有技能, 而不是添加近似重复。 如果该想法与已有技能重叠, 请编辑该技能, 而不是添加新目录 。
- 留着 `SKILL.md` 内部 [docs/skill-anatomy.md](../../docs/skill-anatomy.md),并且绝不在技能之间重复内容,而是参考其他技能。

Conformation.md是整个工作流程唯一的真相来源;这一规则指向它,而不是重述其核对表.
