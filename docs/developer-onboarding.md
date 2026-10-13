# 开发者登机

 这个指南是给工作的人用的 **打开** 代理技能存储器本身: 贡献技能、 修复文件、 改进 eval 带。 如果您想要的话 。 *使用情况*  在自己的项目中,你正在寻找 [启动( M)](getting-started.md) 换了它。

 这是导游,不是规则手册 规则在其中存在 [捐款额(美元)](../CONTRIBUTING.md) (贡献工作流程), [技术-外科](skill-anatomy.md) (技能格式),以及 [evals/README.md](../evals/README.md) (eval 框架); 此文档告诉您何时阅读每个文件, 以及棋子如何匹配 。

---

## 1. 心理模式

Repo有五个可混凝土层 了解每个层是什么 *(单位:千美元)* 防止最常见的贡献错误 (将参考材料运用到技能中,培养一个通向他人的人a,在技能上重复内容).

| 层 | 地点 | 任务 |  一个字 |
|---|---|---|---|
| **技能** | `skills/<name>/SKILL.md` | 附有核查门的分步工作流程 | *怎么样?* |
| **个人** | `agents/<role>.md` | 带有视角和产出格式的角色 | * 谁* |
| **图标** | `.claude/commands/`, `.gemini/commands/`, `commands/` | 面向用户的切入点; 管线层 | *何时* |
| **参考资料** | `references/*.md` | 核对表技能随需求而调动 | *检查什么?* |
| **爱瓦尔斯** | `evals/cases/<name>.json` | 证明这种技能触发和行为正确 | *有用吗?* |

两项结构规则值得早期内化:

- **用户 (或斜线命令)  是管弦乐的** Personas 从未引用他人;唯一得到认可的多人模式是并行扇形输出和合并步骤 ( 见 [references/orchestration-patterns.md](../references/orchestration-patterns.md)).
- ** 不要重复,参考。** 技能与其他技能的联系 `references/` 重述内容。同样的规则也适用于文件,包括本规则。

其中一个范围提醒人们: `AGENTS.md` 财务报告和财务报告 `CLAUDE.md`  在 repo 根中配置工作代理人 * 此还原*。它们不是可重复使用的资产,设置指南绝不能告诉用户将其复制到自己的项目中;可重复使用的资产是技能。

注意命令存在于三个并行目录中 (Claude Code, Gemini CLI反重力)触摸一个和 CI  见§3, 检查所有这些国家的平等情况。

## 2. 地方设置

```bash
git clone https://github.com/addyosmani/agent-skills.git
cd agent-skills
```

 没有阶梯,没有 `package.json`; 验证器是纯节点脚本。您需要 :

- **节点 20+** (怎么样? CI 运行)  联 合 国 `scripts/` 校验符
- **狂欢吧** (+ `jq`  建议) 用于钩式回归测试
- **`gh` CLI**  在提议技能之前进行重复的PR检查
- **Claude Code** 只有在本地运行第三级行为评估时

尝试在现场直播 与本地的退票:

```bash
claude --plugin-dir /path/to/agent-skills
```

## 3. 核查循环

Repo吃自己的烹饪:对技能的核查是不可谈判的,对Repo的贡献也是不可谈判的,一切. CI 运行,您可以在秒内本地运行 :

```bash
# Tier 1, structural: frontmatter, naming, required sections
node scripts/validate-skills.js

# Command parity and description sync across the three command directories
node scripts/validate-commands.js

# Tier 2, trigger & routing: positive prompts rank top-k, negatives don't collide
node scripts/run-evals.js

# Tier 3, behavioral (on demand, spends tokens; --dry-run prints the plan)
node scripts/run-evals.js --behavioral <skill-name> --dry-run

# Hook regression test, required if you touch hooks/session-start.sh
# or skills/using-agent-skills/SKILL.md
bash hooks/session-start-test.sh
```

 即使你从未触碰过绳子 三个等级也是值得理解的 因为红色的二级通常意味着 *修补你的技能描述*,而不是 eval: 第2级是路线的词典近似值 (生成 TF- IDF 超过描述),它的两个目标失败模式是缺少词汇用户实际表示的描述,以及超过正确技能的超宽描述。完整的设计、计划以及信任级规则都在其中。 [evals/README.md](../evals/README.md).

在每个 PR 之前运行相关的子集。 通过 1 + 2 + 命令等值到达绿色的 PR 是可审查的; 在读取内容之前, 它不会反弹到力学上 。

## 4. 贡献途径

### 途径1:修补或提高现有技能 ( 最常见,最好的第一 PR)

1. 保持变化的焦点和最小化;保持技能的结构和语气.
2.  如果你改变了前题 `description`,预计第二级效果;运行 `node scripts/run-evals.js` 并检查技术的触发提示 仍然排序。
3. 运行一级以确认前题仍然有效 。

### 途径2:提出新的技能 (高点的酒吧,做飞行前)

 该目录已经覆盖了大部分生命周期,因此举证责任在空白。在撰写任何文件之前,请输入飞行前的检查。 [捐款额(美元)](../CONTRIBUTING.md#before-proposing-a-new-skill): 搜索目录, 检查打开的 PR (`gh pr list --state open`; 已存在近似重复的集群),确认这个想法符合 [技术-外科](skill-anatomy.md),并明确证明您在 PR 描述中的空白。如果它与现有技能重叠,则该技能的焦点编辑会比新目录更胜一筹。

一个新的技能船作为一组,而不是一个单一的文件: `skills/<kebab-case-name>/SKILL.md`,一个匹配 `evals/cases/<name>.json`和 a 键 `scripts/` 仅当目录载运可运行的帮助者时 (参考材料进入 `references/`永远不要在技能中)精确的前题规则 解剖学 和Eval-case的最小值 [捐款额(美元)](../CONTRIBUTING.md#structure) 财务报告和财务报告 [技术-外科](skill-anatomy.md);从那里取来,而不是这次巡演,所以两人不会漂流.

值得内化而不是抬头的一点:在写入触发提示时,解释用户如何实际说话;将描述复制到提示游戏中,eval并告诉你什么.

### 路径 3: 文档、参考文献、调用

- 医生和技能 **仅英文**翻译因为漂移而不被接受 ([捐款额(美元)](../CONTRIBUTING.md#translations) 说明理由).
- 变动至 `scripts/run-evals.js` 或 eval 计划 应与 技能创造器 兼容 。 `evals.json` 计划 (用于行为级的已采用逐字记录; 兼容性是一个特征, 而非意外).
- 任何触摸会话启动钩或它嵌入的元技能,都需要进行钩子回归测试. (§3).

## 5. PR前核对表

- [ ] 1级绿色 : `node scripts/validate-skills.js`
- [ ]  第二级绿色 : `node scripts/run-evals.js`
- [ ]  如果您触及到任何命令目录, 命令等同为绿色 : `node scripts/validate-commands.js`
- [ ] 钩测试绿色,如果你触摸 `hooks/` 或 `using-agent-skills`
- [ ]  新技能 – 尽可能少地提供 eval 案卷 trigger/behavioral 计数
- [ ] 新的技能 + 公关说明中的空白; 已检查目录和公开公关
- [ ]  没有重复的内容;取而代之的是相互参照
- [ ] 变革规模小,重点突出 ( {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}雷波的自已 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你应该知道 `code-review-and-quality` 变化大小指导也适用于此处的贡献)

## 6. 建议的阅读顺序

1. [读取M.md](../README.md): 目录和生命周期图 (10分钟)
2. `skills/using-agent-skills/SKILL.md`: 线程如何从代理方运行
3.  一个成熟的技巧结束 (譬如说. `test-driven-development`):以实例将解剖学内化
4. [技术-外科](skill-anatomy.md): 格式 spec, 现在带有上下文
5. [evals/README.md](../evals/README.md): 三级和大小写格式
6. [捐款额(美元)](../CONTRIBUTING.md) + [助理人员.md](../AGENTS.md): 规则和重新扫描的代理配置
