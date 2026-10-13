#  其他东道主

 这些主机安装包但不在 README 的安装区段, 因为没有任何维护者运行它们上的当前放行。 每个条目都是一行: 主机及其安装命令。 要添加一个, 请参见 [添加主机指南](../CONTRIBUTING.md#adding-a-host-guide).

技能是简单的 `SKILL.md` 文件夹,所以任何读取 Agent Skills  布局可以装入它们。见 [启动( M)](getting-started.md).

- **Dojo 工作空间**: `dojo skills add addyosmani/agent-skills`
- **fx (单位:千美元)**: `git clone https://github.com/addyosmani/agent-skills.git /tmp/agent-skills && mkdir -p .fx/skills && cp -R /tmp/agent-skills/skills/* .fx/skills/` ( 项目范围; 使用 `~/.fx/skills/` 用于每个工作空间; 需要 fx 0. 5 或以后)
- ** 哦,我的皮尔**: `omp plugin marketplace add addyosmani/agent-skills`,则 `omp plugin install agent-skills@addy-agent-skills`
- ** ⁇ **: `pi install git:github.com/addyosmani/agent-skills`
