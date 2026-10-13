---
description: A test-first request that belongs to test-driven-development. The review skill must not fire.
expected_outcome: The reply proposes a failing test first; code-review-and-quality is never invoked.
max_turns: 12
allowed_tools: [Read, Glob, Grep, Skill]
---

 在我们修复之前为这个错误写一个失败的测试 : 拆分键(100, 3) 返回时 [33, 33, 33] 少了一分钱
