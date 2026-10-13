---
description: Run TDD workflow — write failing tests, implement, verify. For bugs, use the Prove-It pattern.
---

调用代理技能:test-driven-development 技术。

 对于新功能:
1. 写入描述预期行为的测试 ( 他们应该去报到)
2. 执行代码让他们通过
3. 保留测试绿色时重构

 对于错误修正 (证明- 它的模式):
1. 写一个复制错误的测试 (必须是错误)
2.  确认测试失败
3. 执行固定
4.  确认测试合格
5. 运行回归的完整测试套件

 对于与浏览器有关的问题,也引用代理技能:browser-testing-with-devtools 与 Chrome DevTools MCP.
