# 利用Windsurf的代理技能

## 设置

###  项目规则

风浪用途 `.windsurfrules` 用于特定项目代理指令:

```bash
# Create a combined rules file from your most important skills
cat /path/to/agent-skills/skills/test-driven-development/SKILL.md > .windsurfrules
echo "\n---\n" >> .windsurfrules
cat /path/to/agent-skills/skills/incremental-implementation/SKILL.md >> .windsurfrules
echo "\n---\n" >> .windsurfrules
cat /path/to/agent-skills/skills/code-review-and-quality/SKILL.md >> .windsurfrules
```

###  全球规则

 对于所有项目中您想要的技能,请加入Windsurf的全球规则:

1. Open Windsurf – 设置 – AI – 全球规则
2. 粘贴您最常用技能的内容

##  建议配置

保留 `.windsurfrules` 重点放在2-3个基本技能上,以保持在背景限度内:

```
# .windsurfrules
# Essential agent-skills for this project

[Paste test-driven-development SKILL.md]

---

[Paste incremental-implementation SKILL.md]

---

[Paste code-review-and-quality SKILL.md]
```

##  用法提示

1. **选择** ——Windsurf的上下文有限. 选择能解决你最大的质量差距的技能.
2. **谈话中的参考文献** ——在特定阶段工作时将更多技能内容粘贴到聊天中. ( 例如,粘贴 `security-and-hardening`  当建立认证时).
3. **使用引用作为核对表** - 粘贴 `references/security-checklist.md` 并要求Windsurf 核实每个项目。
