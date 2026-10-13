# 使用带有指令代码的代理技能

[命令代码](https://commandcode.ai) 拥有本土技能系统。 `cmd skills` 命令复制 a GitHub  回波, 递归性地发现每个 `SKILL.md`,并安装你选的。

命令代码二进制为 `cmd` (带有别名 `cmdc` 窗口和 `command-code`)。以下示例使用 `cmd`.

## 安装

** 项目范围** (安装到 `.commandcode/skills/` 在当前 git 根中 — 默认值):

```bash
cmd skills add addyosmani/agent-skills
```

 在交互式终端中, 显示一个多选择, 这样您就可以选择要安装的 25 个技能 中哪一个 。 Pipe/non-interactive 引用安装所有发现的技能.

**安装特定技能 :**

```bash
cmd skills add addyosmani/agent-skills -s spec-driven-development
```

**用户范围** (安装到 `~/.commandcode/skills/`,在每个项目中提供):

```bash
cmd skills add addyosmani/agent-skills --global
```

** 其他支持的表格 :**

```bash
cmd skills add addyosmani/agent-skills@main            # a specific branch
cmd skills add addyosmani/agent-skills/skills/interview-me   # a specific path in the repo
cmd skills add addyosmani/agent-skills --force         # overwrite / update if already installed
```

## 管理

```bash
cmd skills list                       # list installed skills (project + user + bundled)
cmd skills remove spec-driven-development           # remove a project-scoped skill
cmd skills remove spec-driven-development --global  # remove a user-scoped skill
```

`--force` 打开 `add` 重排并覆盖已有的技能,这就是您如何更新到最新版本。

## 使用量

自动发现安装的技能,并在 TUI 斜线菜单中显示, 标记 `[skill]`:

```
/spec-driven-development   [skill] Write a spec before writing code…
```

类型 `/` 来浏览,或开始输入要过滤的技能名称。使用 `/skills` 改为: enable/disable 技术。

## 技能所在地区

命令代码从这些地点发现技能 (以最近的 git 根解析项目条目):

| 范围 | 路径 |
|-------|------|
|  项目 | `.commandcode/skills/<name>/SKILL.md` |
|  项目 (代理- compat) | `.agents/skills/<name>/SKILL.md` |
| 用户 | `~/.commandcode/skills/<name>/SKILL.md` |
| 用户 (代理- compat) | `~/.agents/skills/<name>/SKILL.md` |

`cmd skills add` 写到 `.commandcode/skills/` ( 项目) 或 `~/.commandcode/skills/` (`--global`).
