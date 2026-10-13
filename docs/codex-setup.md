# 使用代理技能与 Codex

 这个寄存器也是 [Codex 插件](https://developers.openai.com/codex/plugins/build)。同样的根级 `skills/` 使用的目录 Claude Code 由下列单位消耗: Codex,因此没有复制或复制文件。

## 安装

```bash
codex plugin marketplace add addyosmani/agent-skills
codex plugin add agent-skills@agent-skills
```

> 需求 Codex CLI  在旧版发布时,命令是: `codex marketplace add`。见 [Codex CLI 文档](https://developers.openai.com/codex/cli).

 第一个命令将此寄存器注册为 `agent-skills`  第二个命令安装并启用 `agent-skills` 市场插件。 启动新程序 Codex 安装后进行会话,以便发现这些技能。

本地克隆人也会工作:

```bash
codex plugin marketplace add /path/to/your/clone
codex plugin add agent-skills@agent-skills
```

## 使用量

安装后,请在 Codex 聊天与 `@` (譬如说. `@spec-driven-development`) 或只是描述任务 并让 Codex 选对了 全部25个技能 `skills/` 备有。

[Codex 使用渐进式披露](https://developers.openai.com/codex/skills):它从每个技能开始 `name` 财务报告和财务报告 `description`,按要求选择技能,然后满载 `SKILL.md` 。不同时粘贴 `using-agent-skills/SKILL.md` 输入 `AGENTS.md`,系统提示,或其他总是在上下文:将包的元路由器堆放在 Codex本地路由器, 并添加不必要的路由工作。 元技能可以保留与包一起安装; 警告是特别反对预装其全部指令的 。

## 如何运作

- `.codex-plugin/plugin.json` — Codex 显示在 repo 根的插件。 点 `skills` 时间: `./skills/` ,并提供 Codex.
- `.agents/plugins/marketplace.json` ——市场进入,宣告回波根 (`./`)  作为插件源。
- `skills/<name>/SKILL.md` ——不变. Codex 财务报告和财务报告 Claude Code 共享同样内容 `name` + `description`  前题格式,所以一个文件为两个平台服务.

斜线命令在 `.claude/commands/` 和人员在 `agents/` 留下来 Claude Code- 特别的,那个 `SessionStart` 脚本在 `hooks/` 是一个独立的帮助器,用于没有本地技能路径的主机,并且没有被两个插件连接。 On Codex,直接引用基础技能,而不是斜线命令 (譬如说. `@spec-driven-development` 改为 `/spec`).
