# 捐款 Agent Skills

感谢您对贡献的兴趣! 该项目是AI编码代理产品级工程技能的集合.

 新来的? [docs/developer-onboarding.md](docs/developer-onboarding.md) 是一个引导巡演 如何重新定位 ( 五层, 校验循环, 以及贡献路径) 告诉你什么时候读这份文件, [技术-外科](docs/skill-anatomy.md),以及 [evals/README.md](evals/README.md)。此文件是权威的规则手册;登机指南是地图。

## 添加新技能

###  在提出新技能之前

 这包已经覆盖了大部分开发生命周期,许多建议与现有的技能或另一个开放式的PR重叠。在打开一个之前,这些检查是否因此审查人员不会重复:

1. **搜目录.** 浏览 [README 中的技能列表](README.md) 和滑动 `skills/` 一种涵盖你想法的全部或部分的现有技能。
2. ** 请检查date=中的日期值 (帮助)** 运行 `gh pr list --state open` (或浏览 PRs 标签) 并寻找关于同一主题的建议 。 已经存在几组近重复的技能; 不要添加这些技能 。
3. **检查否决的提案。** 搜索该 [技能变动拒绝分类账](evals/skill-impact.md) 与你的想法相重叠的早期建议,并在重复工作之前审查它们的证据。
4. **读解剖学.**  确认您的想法符合格式 [docs/skill-anatomy.md](docs/skill-anatomy.md),一个可操作的工作流程 与核查,而不是模糊的建议。
5. **说明你公关的漏洞** 说明为什么这没有被现有技能、公开公关或先前被拒绝的建议所涵盖。 如果建议重叠,则提议扩大现有技能,而不是增加新的技能。
6. **脱衣舞女郎**  如果一个步骤不能不命名一个模型,一个模型版本,或者一个代理的私人工具名称来证明它是正当的,那么它就不属于一个技能——描述这个能力. ( 见 [写程序,而不是工作](docs/skill-anatomy.md#write-the-procedure-not-the-workaround)).

 如果您的想法是对现有技能的完善,则选择重点编辑而不是该技能,而不是新的目录。

### 创造技能

1.  在下方创建目录 `skills/` 带有 kebab 案件名
2. 添加一个 `SKILL.md` 遵循格式 [docs/skill-anatomy.md](docs/skill-anatomy.md)
3. 包含 YAML  前题 `name` 财务报告和财务报告 `description` 字段,且顶级没有销售商或运行时间密钥 ( 这些都沉下去 `metadata` 或每个代理适配器文件;见 [docs/advanced-per-agent-configuration.md](docs/advanced-per-agent-configuration.md))
4. 保证 `description` 开始于技能的作用 ( 第三人称),然后包括一个或多个 `Use when` 触发条件

### 技能质量栏

技能应该是:

- **具体** - 可采取行动的步骤,而不是含糊不清的建议;
- ** 可核实** - 有证据要求的明确退出标准
- **作战测试** ——基于真实的工程工作流程,而非理论理想.
- **最小数** ——只有正确指导代理人所需的内容.

### 结构

每种新技能都必须有:

- `SKILL.md`  在技能目录中
- YAML  前题为有效 `name` 财务报告和财务报告 `description`
-  一个 eval 案件档案 : `evals/cases/<skill-name>.json` - 至少3个正触发器,2个负触发器 (与 `owner` 可能时),和 1 行为 eval 。执行 eval 必须有真实文件支持。 `evals/fixtures/`; 对话形状的技能可能使用评审员指定 `kind: "dialogue"` 换成 eval ( 见 [evals/README.md](evals/README.md)). CI 强制执行这些要求。

新的技能一般应该遵循标准的解剖学:

- **概览** ——这种技能有什么作用,为什么重要?
- **何时使用** - 触发条件
- **进程** ——分步工作流程.
- **共同合理化** ——借口特工经常跳过台阶,有反驳.
- **红旗** ——警告显示该技能应用不正确
- **核查** - 如何确认技能得到正确应用

上面的前题字段是需要的。 解剖学部分是一个建议的模式: 等效标题, 如 。 `How It Works`, `Workflow`,或 `Core Process`  当他们保持同样的意图,并让技能易于遵循时,他们就很好。

### 何谓不做

-  不要在技能之间重复内容——参考其他技能
-  不要增加那些模糊的建议 而不是可操作的程序
-  除非内容超过100行,否则不要创建辅助文件
-  不要创造空的 `scripts/`  目录只是为了匹配另一个技能——添加 `scripts/`  只当技能包括可操作的助手时
-  不要在技能目录中添加参考材料——使用 `references/` 换成

## 修改现有技能

 在提出修改之前,请搜索 [技能变动拒绝分类账](evals/skill-impact.md) (c) 对以前影响相同技能的尝试进行审查,并审查它们的证据。

- 保持重点突出和最小化的更改
- 保留现有结构和语气
- 试一下 YAML  前题在编辑后仍然有效

 如果基于eval结果拒绝了技能或描述变化,则在分类账中加入一行并注明日期,影响技能,简洁的尝试改变,在排名1之前的分数,并拒绝公关链接和结果。 仅该分类账对默认分支进行单独更新;不只保留在被否决的建议书分支,因为关闭或强行删除建议书可能会丢弃记录。

## 添加主机指南

README 安装区域列表中只包含一个维护者已安装并运行当前发布。 所有其他主机都会在其中获得一行 [docs/other-hosts.md](docs/other-hosts.md):主机名称及其安装命令。没有设置专用页面、供应商链接、标识、emoji或截图。

-  如果你为主机背后的公司工作,在公关里就这么说.
-  一个主机一旦维护者运行了它,并愿意保持其条目流,就会移动到README中.
-  对主机指南按打开顺序进行审查。

## 重置范围的文件

`AGENTS.md` 财务报告和财务报告 `CLAUDE.md`  在 repo 根中配置在 [`addyosmani/agent-skills`](https://github.com/addyosmani/agent-skills)  当编写设置指南或文件时,不指示用户将这些文件复制到自己的项目或全局代理配置中;可重复使用的资产是以下技能: `skills/`.

## 翻译

 我们不接受文件的翻译 (读我, `docs/`) 翻译的拷贝随着技能和文件的发展而流出,如果不依靠代理翻译以及社区矫正,那么我们就无法长期保存这些拷贝,而社区矫正又会增加维护成本,但价值有限。所有技能、文件和贡献都用英语。

## 测试钩子

 会话启动脚本 (`hooks/session-start.sh`) 输入 `using-agent-skills` 接入主机时的元技能 `SessionStart` 钩子 Claude Code 插件不注册它 - Claude Code 本土的路由技能,而且始终注射,将为同一任务创建两个路由器 ( 见 [docs/getting-started.md](docs/getting-started.md)); 脚本保留给没有本土技能路由的主机。回归测试在 `hooks/session-start-test.sh` 校验脚本 JSON 有效载荷 -- -- 当 `jq`  当它不可用时。

 在打开任何触摸到的公关前运行它:

- `hooks/session-start.sh`
- `skills/using-agent-skills/SKILL.md` (钩子嵌入的元技能内容)

```bash
bash hooks/session-start-test.sh
```

 预期产出: `session-start JSON payload OK`。在任何断言失败时,脚本退出非零。

### 复制无jq倒置

钩子还是发音一样 `hookSpecificOutput` 信封时 `jq` 还没上 `PATH`,与 `additionalContext` 解释: `jq`  为了在当地锻炼该分支,剥离 `jq`'目录从 `PATH` 用于试验:

```bash
JQ_DIR=$(dirname "$(command -v jq)")
PATH=$(echo "$PATH" | tr ':' '\n' | grep -v "^${JQ_DIR}$" | tr '\n' ':' | sed 's/:$//') \
  bash hooks/session-start-test.sh
```

 这工作很干净,当 `jq` 生活在自己的目录中 (譬如说. `/opt/homebrew/bin`  从家乡来的, `/usr/local/bin`  从手动安装)。如果选中的话。 `jq` 与测试所依赖的其他工具共享一个系统 bin ( 例如, `mktemp` 输入 `/usr/bin`),更简单的方法是安装 `jq`  通过单独的软件包管理器,它拥有自己的bin目录,然后重新运行.

钩子 `command -v jq` 检查失败于剥离 `PATH`测试表明, `jq is required` 指导 `additionalContext`  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}而不是元技能的躯体 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}是的

## 报告问题

 如果您找到, 请打开一个问题 :

- 提供不正确或过时指导的技能
- 缺少共同工程工作流程的覆盖面
- 技能之间的不一致

 如果一个技能的指导错误,过时,或者在您的项目中没有应用
( 例如,它假设 `npm test`  在 Maven 或 Gradle 重播中)时,使用
[技能差距](https://github.com/addyosmani/agent-skills/issues/new?template=skill-gap.yml)
 它要求受影响的技能、相关的节选、您的项目
上下文,以及你所做的事——足以使维护者在不进行分类的情况下进行分解
freeform 写入。

## 许可证

 通过捐款,您同意您的捐款将获得以下许可: MIT 驾照
