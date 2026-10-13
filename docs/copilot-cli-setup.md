# 使用代理技能与 GitHub Copilot CLI

独立 `copilot` 命令行工具将此寄存器安装为插件并发现每种技能 `skills/`。用于内部的副驾驶 VS Code,见 [副驾驶设置.md](copilot-setup.md)  相反,设置和引用模式是不同的。

## 安装

** 从这个仓库的市场** - 注册,然后从它安装。 `addy-agent-skills`  是此寄存器所声明的市场名称,而不是 GitHub- 登记范围更广,名称只在 `marketplace add`:

```bash
copilot plugin marketplace add addyosmani/agent-skills
copilot plugin install agent-skills@addy-agent-skills
```

**直接从存储器**,而不注册市场:

```bash
copilot plugin install addyosmani/agent-skills
```

** 从本地克隆**,用于安装会话范围开发:

```bash
git clone https://github.com/addyosmani/agent-skills.git
copilot --plugin-dir /path/to/agent-skills
```

`--plugin-dir`  只装入该会话的插件, 并且不持续安装任何设备 —— 在编辑技能时使用它 。

## 校验

```bash
copilot plugin list   # agent-skills@addy-agent-skills
copilot skill list    # the plugin's skills, alongside the built-in ones
```

 在一次互动会议上, `/skills list` 显示相同的目录。

##  你得到的,你没有的

词根 `plugin.json`  是显示的副驾驶 CLI ——它优于 `.claude-plugin/plugin.json`,属于 Claude Code。该根表单只声明一个名称、版本和描述,因此组件路径回到默认值:

- **技能——可用。**  没有明确的路径, CLI 使用常规 `skills/` 目录, 以及在那里发现的技能 。
- **生命周期命令——没有。** 根表上没有 `commands` 字段,所以没有注册 `/spec`, `/plan`, `/build`, `/test`, `/review` 或 `/ship`。这些文档存在于 `.claude/commands/` 并且现在 Claude Code 命令时,请输入命令。

显示优先级和每个组件路径的默认值,请参见 [CLI 插件引用](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-plugin-reference) 财务报告和财务报告 [创建插件](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating).

## 使用量

指定您想要的技能, 或描述任务并让代理路径到它 :

> 使用该 spec-driven-development 技能来写入 spec for [特性].

> 使用该 test-driven-development 技能:先为这个错误写一个失败的测试,然后修正它。

> 使用该 code-review-and-quality  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}能回顾一下我所演的变迁 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你觉得呢?

## 解决问题

| 症状 | 检查什么? |
|---------|---------------|
| `plugin install` 无法解决 `agent-skills@addy-agent-skills` | 运行 `copilot plugin marketplace add addyosmani/agent-skills`  首先,这个名称只有在市场注册后才能解决。或者直接安装寄存器。 |
| 插件已安装,但没有技能 | `copilot plugin list` 以确认插件,然后 `copilot skill list` (或 `/skills list`  会话中)  看看发现了什么 |
| `/spec`, `/build` 和朋友没有找到 | 期望, 不是损坏的安装 : 根表单没有记录命令 。 按名称询问技能 。 |
| 本地技能有所改变,但 CLI 显示旧副本 | 开始新会话, 或运行其中 `--plugin-dir` 指着你的克隆人 |
