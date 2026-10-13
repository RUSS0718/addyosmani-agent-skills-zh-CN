# 使用代理技能与 Antigravity CLI (动静)

 那个... `agent-skills` 软件包可以安装为本地插件 Antigravity CLI (`agy`),使代理进入结构化的工作流程和人物。

## 设置

### 备选案文1:原生插件安装 ( 建议)

Antigravity CLI  有头等舱 [插件系统](https://www.agy.dev/docs/plugins/) 存储器还带有遗留的命令TOML,但受到影响 `agy` 释放时不显示其已转换的包装;见 [工作流程和命令兼容性](#lifecycle-workflows-and-command-compatibility).

** 从远程仓库安装 :**

```bash
agy plugin install https://github.com/addyosmani/agent-skills.git
```

** 从本地克隆安装 :**

1. 清除仓库 :
   ```bash
   git clone https://github.com/addyosmani/agent-skills.git
   ```
2. 使用插件安装 `agy`:
   ```bash
   agy plugin install /path/to/agent-skills
   ```

 这将验证插件并将其安装到您的全局反重力配置目录 (`~/.gemini/config/plugins/agent-skills/`).

> **说明:** 在当前异常状态下释放插件区域 `~/.gemini/config/plugins/`而不是遗产 `~/.gemini/antigravity-cli/plugins/` 路径。如果在遗留路径没有看到插件,请检查 `~/.gemini/config/plugins/agent-skills/`  先说.

### 备选案文2:从 Gemini CLI

 如果您已经安装 `agent-skills`  在你的传承下 Gemini CLI 安装后,您可以直接导入:
```bash
agy plugin import gemini
```

安装后, 验证活动插件 :
```bash
agy plugin list
```

---

## 工作流程和命令兼容性

抗重力 [移徙工具](https://www.agy.dev/docs/cli/gcli-migration/)  * 报告9项遗留定义。 `commands/*.toml` 被"转换成技能" 在受影响的。 `agy` 1.1.x 释放, 验证成功, 但转换后的包装没有出现在斜线命令或技能目录中。 成功 。 `agy plugin validate`  因此,证实档案的形成良好,而不是 `/build` 并提供了其他短包装。 [代理技能# 445](https://github.com/addyosmani/agent-skills/issues/445) 和上游 [反重力- cli # 788](https://github.com/google-antigravity/antigravity-cli/issues/788).

直接使用本地插件技能, 而导入限制适用 :

| 预定包装 | 直接反重力引用 |  页:1 |
|------------------|-------------------------------|-------|
| `/spec` | `/agent-skills:spec-driven-development` |  在代码前写入结构化的光谱 |
| `/constraints` | `/agent-skills:constraint-driven-development` | 定义并强制执行项目的质量栏 |
| `/planning` | `/agent-skills:planning-and-task-breakdown` | 反重力是内置的 `/planning` 命令是单独的计划模式控制 |
| `/build` | `/agent-skills:incremental-implementation` | 也援引 `/agent-skills:test-driven-development`; 仅包件 `/build auto`  没有乐谱 |
| `/test` | `/agent-skills:test-driven-development` | 运行红绿源工作流程 |
| `/review` | `/agent-skills:code-review-and-quality` | 运行五轴审查工作流程 |
| `/code-simplify` | `/agent-skills:code-simplification` |  在不改变行为的情况下简化 |
| `/ship` | `/agent-skills:shipping-and-launch` | 包装器的自动人形不可用; 单独调用专家代理 |
| `/webperf` | 选择 `web-performance-auditor` 发件人 `/agents` |  这个工作流程是一个人,而不是一个技能 |

 不添加( N) YAML  前题到 TOML 文档作为工作。 Gemini CLI 读取平行 TOML 带有严格解析器的命令格式,以及 `---`  前题使得这些文件无效 TOML  不改变反重力的转换行为。

---

## 技能发现( D)

反重力自动发现插件内的技能 `skills/`  目录。
* 反重力将用户的任务和意图与相关技能按需匹配.
*  如果任务与技能相匹配, 代理将加载该技能, 并提示您在执行前获得许可 。

---

## 校验和验证

 要验证您的本地插件结构正确且包含所有技能, 请运行 :
```bash
agy plugin validate /path/to/agent-skills
```

 然后开始新的会话和类型 `/agent-skills:` 以检查命名空格技能目录。如果验证器报告命令已转换,则 `/build` 返回时 `No matches`,使用上述直接引用;重新合并或添加a; TOML `name` 字段不能解决已知的进口商限制。

---

## 如何运作

### 1. 待命技能激活
Antigravity CLI 自动发现 `SKILL.md` 位于该区的文件 `skills/` 已安装插件的目录。 使用每个技能的前题中的触发描述, 当代理检测到匹配的开发者意图时, 代理将动态激活相应的工作流程 。

 例如,当您要求代理人:
- **设计新系统** rarr; 将会 suggest/activate `spec-driven-development`.
- **执行特性** rarr; 将激活( R) `incremental-implementation` 财务报告和财务报告 `test-driven-development`.
- **修补错误** rarr; 将激活( R) `debugging-and-error-recovery`.

### 2. 专门代理人
插件登记可重复使用的子剂定义 `agents/`  目录 :
- `code-reviewer.md`
- `security-auditor.md`
- `test-engineer.md`

 您可以在会话中直接引用这些人物, 或者在使用子代理授权任务时引用这些人物 。

---

## 配置自定义( C)

###  项目 -- -- 具体执行 (`AGENTS.md`)
严格遵守技能规定 ( 例如,在写代码之前需要一个规格或计划),复制或链接 `AGENTS.md` 输入您工作空间的根。 Antigravity CLI 读取此文件,使代理的行为和规划阶段与你的团队的常规一致.

### 沙盒模式
 如果您想要运行具有有限终端权限的技能或脚本 ( 在进行第三方验证测试时的安全性),启动 CLI 改为:

```bash
agy --sandbox
```

---

##  用法提示

1. **保持插件的更新 :**  你可以更新 CLI 或检查新插件版本使用 :
   ```bash
   agy update
   ```
2. **执行前的审查:**  当代理员使用这些技能执行复杂的重构任务时,使用 `Ctrl+r` 输入 **艺术审查** 屏幕,用于在代码被承诺之前审查、编辑或批准代码。
3. **控制权限 :**  你可以用这个 `--dangerously-skip-permissions` 标记只存在于您想要绕过手动工具批准提示的可信任的本地工程中。
