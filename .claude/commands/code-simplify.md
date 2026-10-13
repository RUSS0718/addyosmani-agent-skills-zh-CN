---
description: Simplify code for clarity and maintainability — reduce complexity without changing behavior
---

调用代理技能:code-simplification 技术。

简化最近更改的代码 (或指定的范围)  在保留准确行为的同时:

1. 阅读CLAUDE.md并研究项目公约
2. 确定目标代码——最近的变化,除非指明范围更广
3. 了解代码的目的,呼叫者,边框, 和测试覆盖之前触摸它
4. 扫描简化机会 :
   - 深筑巢 – 护卫条款或提取的助手
   - 长期职能-按责任划分
   - 巢穴 □ if/else 或切换
   - 通用名称 − 说明性名称
   - 重复逻辑 – 共享函数
   - 死亡代码 – 在确认后删除
5. 递增应用每次简化——每次更改后运行测试
6. 检查所有测试通过, 建设成功, 和diff是干净的

 如果在简化后测试失败, 请返回更改并重新考虑。 使用 `code-review-and-quality` 以审查结果。
