#  会议背景审计

寄存器是一个 TypeScript 服务。当前代理会话加载
整个 `docs/archive/`  目录 (1 800个文件),生成 API 输出, 六旧
事件记录簿,以及每份 ADR  在启动时。它不装入活动
`CONTRIBUTING.md` 或 `docs/current-architecture.md`.

观察到的失败 :

-  建议的答复 JavaScript  即使新的来源必须是 TypeScript.
- 试验建议与 Jest,但该项目使用 Vitest.
- 特工屡次忘记数据库访问属于寄存器.
- 答案在长的工具痕迹后成为通用的.

 当前的任务: 将校验添加到一个已有的任务中 HTTP 管理者。
