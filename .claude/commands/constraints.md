---
description: Define and enforce this project's quality bar — interview, sane defaults, CONSTRAINTS.md
---

调用代理技能:constraint-driven-development 技术。

目标

 无参数的默认行为: 为此寄存器设置限制 。

1. **检测第一。** 读取 package.json / pyproject.toml / go.mod,测试跑步器,已有的lint配置,当前覆盖输出, CI 工作流程, 以及正在使用的代理设备。 报告您在两行中发现的东西。 永远不要要求您读到的东西 。

2. **面试,最多四个问题。** 一次一次一次,每个都有你最好的猜测和一个可用的默认,所以"我不知道"仍然产生一个工作配置:
   - 楼层以外的哪个维度 (覆盖面、安全、性能、无障碍环境、结构)
   -  当检查失败时屏蔽或警告
   - 目标数,或测量今天的值并持有它们
   -  在将工作交还之前, 允许最慢的检查

3. **写入 CTRINTS. md** 。每个数字需要说明理由。

4. **安装每个选中的尺寸需要的 。** 带有数字且后面没有工具的维度是一个愿望。 使用事实上的工具, 使已有的配置继续工作 : semgrep 用于代码扫描、 gitleaks (永远 `--redact`) 用于机密, Osv- scanner 用于依赖性, 用于访问的轴- 核心, 用于网络要害的灯塔, 用于捆绑的大小限制, 用于边界的依赖- cruiser, 用于断言质量的 Stryker。 请在 CONSTRAINTS. md 中记录每个规则旁边的确切命令 。 URL; 如果工程没有, 请说, 并降低尺寸, 而不是发明检查。 添加命令到 package.json 作为 check: fast / check: task / check: full 。

5. ** 每张支票都要按成本计** 编辑循环中的类型、提示和秘密 (秒数)。在任务端进行相关测试和换行覆盖 (90岁以下)复议或复议时的其他一切 CI范围检查到diff,而不是整个Repo。

6. **指着特工**  在CLAUDE.md中添加一行,告诉特工们去读读 CONSTRAINTS.md 并且永远不要削弱它来做一个改变的通过.

7. **校验** 运行当前分支的限制 。 如果用户不同意任何失败, 现在就解决限制 , 而不是离开一个大门 。 人们会学会忽略 。

子命令 :
- `/constraints check` 管理目前对该分支机构的限制,并提出报告。
- `/constraints guard` 检查diff的栏:降低阈值、跳过或删除测试、新的压制评论、未完成的柱子、新的例外
- `/constraints ratchet` ——记录今天测量的数值为不能跌落的底数.
