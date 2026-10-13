# 代理技能

 这是代理技能项目——AI编码剂的生产级工程技能集.

> **范围:**  此文件将配置正在运行的代理程序 [`addyosmani/agent-skills`](https://github.com/addyosmani/agent-skills) 存储器本身,而不是其他项目。不要将其复制到另一个项目或全局代理配置中;可重复使用的资产是技能 `skills/`.

##  项目结构

```
skills/       → Core skills (SKILL.md per directory)
agents/       → Reusable agent personas (code-reviewer, test-engineer, security-auditor, web-performance-auditor)
hooks/        → Session lifecycle hooks
.claude/commands/ → Slash commands (/spec, /plan, /build, /test, /review, /code-simplify, /ship; plus /webperf specialist audit)
references/   → Supplementary checklists (testing, performance, security, accessibility, observability)
evals/        → Skill eval cases + framework (see evals/README.md)
docs/         → Setup guides for different tools
```

##  按阶段分列的技能

**定义 :** interview-me, idea-refine, spec-driven-development
**计划:** planning-and-task-breakdown
**构建 :** incremental-implementation, test-driven-development, context-engineering, source-driven-development, doubt-driven-development, frontend-ui-engineering, api-and-interface-design
**校验 :** browser-testing-with-devtools, debugging-and-error-recovery
**检讨:** code-review-and-quality, code-simplification, security-and-hardening, performance-optimization
**舰只:** git-workflow-and-versioning, ci-cd-and-automation, deprecation-and-migration, documentation-and-adrs, observability-and-instrumentation, shipping-and-launch

## 公约

- 每一个技能都生活在 `skills/<name>/SKILL.md`
- YAML  前题 `name` 财务报告和财务报告 `description` 字段
- 说明从技能开始 ( 第三人称),然后是触发条件 ("使用当...")
-  每个技能都有: 概览、何时使用、进程、共同合理化、红旗、核查
- 共享参考文献在根中 `references/`  目录; 新出现的关于自足、可分配技能的公约将技能本身的参考文献保留在其中 `skills/<name>/references/`
- 仅当内容超过100行时才会创建支持文件

## 捐款

 在增加新技能或对现有技能进行重大重新工作之前,先进行飞行前检查。 [捐款额(美元)](CONTRIBUTING.md#before-proposing-a-new-skill): 搜索目录, 检查打开的公关, 确认想法符合 [docs/skill-anatomy.md](docs/skill-anatomy.md),并证明这一差距是合理的。更倾向于扩展现有技能,而不是添加近似重复。Conformation.md是本工作流程唯一的真相来源;不要在此或其他地方重复其核对表,与之链接。

## 图标

- `npm test` - 不适用 ( 这是一个文档项目)
- 校验: 检查所有 SKILL. md 文件是否有效 YAML 名称和描述的前题
- 爱瓦尔斯: `node scripts/run-evals.js` — trigger/routing 每种技能的测试 (CI); `--behavioral <skill>` 分级运行

## 调用请求

PRs 瞄准上游寄存器的默认分支。 在典型的叉设置中, 上游远程是 `upstream`  而你们的叉子 `origin` 但确切的远程名称并不重要

-  在打开 PR 之前, 搜索上游寄存器的公开 PR 和 涉及相同文件或规则的工作的问题 。 如果有重叠, 请协调 。 ( 在它的基础上, 调整您的规则, 或者在它合并后重新建立) 而不是打开一个矛盾的公关。
- 偏好小型、重点突出的公关而不是广泛共享文件的大型重构件 ( 例如,下面的文件 `scripts/`),它们更可能与飞行中的工作相撞。

## 边界

- 总是: 在创建新的技能目录前运行 Controduction.md 飞行前检查
- 永远: 遵循技能- 解剖. md 格式以获得新技能
- 总是: 在打开新的 PR 之前检查上游 repo 的开放式 PR 和 重复 问题
- 永不: 添加模糊建议而不是可操作进程的技能
- 永远: 重复技能之间的内容——转而参考其他技能
