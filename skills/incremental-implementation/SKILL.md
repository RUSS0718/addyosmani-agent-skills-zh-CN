---
name: incremental-implementation
description: Delivers changes incrementally in thin, verifiable slices. Use when implementing any feature or change that touches more than one file, or when picking up the next task from a plan. Use when rolling a change out behind a feature flag, when you're about to write a large amount of code at once, or when a task feels too big to land in one step.
---

# 递增执行

## Overview


以薄的垂直切片构建 — 执行一块, 测试, 验证, 然后扩展 。 避免在一个通道中执行整个特性 。 每次递增应该让系统处于工作, 可测试的状态 。 这是执行的学科, 使得大型特性可以管理 。

## When to Use


- 执行任何多文件更改
-  通过任务分类构建新特征
- 重构已有代码
- 每当你尝试在测试前写超过~100行

** 不使用时 :** 单文件,单功能在范围已经最小的地方变化.

## 增量循环

```
┌──────────────────────────────────────┐
│                                      │
│   Implement ──→ Test ──→ Verify ──┐  │
│       ▲                           │  │
│       └───── Commit ◄─────────────┘  │
│              │                       │
│              ▼                       │
│          Next slice                  │
│                                      │
└──────────────────────────────────────┘
```

 每个切片 :

1. **执行** 最小的完整功能块
2. **测试** - 运行测试套房 ( 如果不存在, 则写一个测试)
3. **校验** ——按预期确认切片工程. (测试通过, 构建成功, 手动检查)
4. **提交** -- 用描述性信息保存您的进度 ( 见 `git-workflow-and-versioning` 原子承诺导引)
5. **移动到下一个切片** ——前进,不要重启.

## 切片策略

### 垂直切片 (首选)

 通过堆栈构建一条完整的路径 :

```
Slice 1: Create a task (DB + API + basic UI)
    → Tests pass, user can create a task via the UI

Slice 2: List tasks (query + API + UI)
    → Tests pass, user can see their tasks

Slice 3: Edit a task (update + API + UI)
    → Tests pass, user can modify tasks

Slice 4: Delete a task (delete + API + UI + confirmation)
    → Tests pass, full CRUD complete
```

 每个切片都提供工作端到端的功能.

### 合同第一次切片

 当后端和前端需要并行开发时:

```
Slice 0: Define the API contract (types, interfaces, OpenAPI spec)
Slice 1a: Implement backend against the contract + API tests
Slice 1b: Implement frontend against mock data matching the contract
Slice 2: Integrate and test end-to-end
```

### 风险第一切片

 首先处理最危险或最不确定的部分:

```
Slice 1: Prove the WebSocket connection works (highest risk)
Slice 2: Build real-time task updates on the proven connection
Slice 3: Add offline support and reconnection
```

 如果切片1失败,在投资切片2和3之前先发现.

## 执行规则

###  第0条:首先简单化

 在写出任何代码之前,问: 什么是最简单的事物可以工作?

 在写出代码后,对照这些检查来审查:
- 能不能少说几句话?
-  这些抽象是获得其复杂性的吗?
-  一个员工工程师会看看这个 说"你为什么不..."吗?
-  我是为了假设未来的需求,还是为了目前的任务?

```
SIMPLICITY CHECK:
✗ Generic EventBus with middleware pipeline for one notification
✓ Simple function call

✗ Abstract factory pattern for two similar components
✓ Two straightforward components with shared utilities

✗ Config-driven form builder for three forms
✓ Three form components
```

 三条相似的代码线比不成熟的抽象更好。 先执行天真、明显正确的版本。 只有在通过测试证明正确后才能优化 。

###  细则0.5:范围纪律

 只接触任务需要的东西。

不许 :
- 与您的更改相邻的“ 清理” 代码
-  在您没有修改的文件中重构导入
-  删除您不完全理解的评论
-  在光谱中添加不包含的特性, 因为它们“ 似乎有用 ”
- 将您只读的文件中的语法现代化

 如果你发现任务范围以外值得改进的东西,请注意——不要修正:

```
NOTICED BUT NOT TOUCHING:
- src/utils/format.ts has an unused import (unrelated to this task)
- The auth middleware could use better error messages (separate task)
→ Want me to create tasks for these?
```

###  第1条:一次一件事情

 每一次递增都会改变一个逻辑问题,别混为一谈:

**错误 :**  一个承诺增加了一个新的组件,重构一个已有的组件,并更新构建配置.

**不错:** 三个不同的承诺——每个变化一个。

###  第2条:保持可编译

 每次递增后,项目必须构建,现有的测试也必须通过。不要在切片之间离开密码库。

###  第3条:不完全特征的特征标志

 如果一个功能还没有准备好供用户使用,但需要合并增量:

```typescript
// Feature flag for work-in-progress
const ENABLE_TASK_SHARING = process.env.FEATURE_TASK_SHARING === 'true';

if (ENABLE_TASK_SHARING) {
  // New sharing UI
}
```

这使得您可以将小增量合并到主分支,而不会暴露不完整的工作.

###  第4条:安全默认

新的代码应该默认为安全,保守的行为:

```typescript
// Safe: disabled by default, opt-in
export function createTask(data: TaskInput, options?: { notify?: boolean }) {
  const shouldNotify = options?.notify ?? false;
  // ...
}
```

###  第5条:退后友好

每项递增应可独立递增:

- 附加变化 ( 新文件, 新函数) 很容易还原
- 对现有守则的修改应尽量少且重点突出
- 数据库迁移应具有相应的回滚迁移
- 避免在一项犯罪中删掉某些东西,在一项犯罪中加以取代——将其分开

## 与代理人合作

指示代理人逐步执行时:

```
"Let's implement Task 3 from the plan.

Start with just the database schema change and the API endpoint.
Don't touch the UI yet — we'll do that in the next increment.

After implementing, run the repository's test and build commands to
verify nothing is broken."
```

明确每个增量的范畴和不范畴的范畴.

## 递增核对表

 每次增量后,请与寄存器自身的命令校验 ( 见 test-driven-development 技能发现堆栈 第一节):

- [ ] 改变有一件事 完全改变
- [ ]  所有现有的测试仍然通过 (存储器的测试命令 : `npm test`, `./gradlew test`, `pytest`, ...)
- [ ] 建造成功 (存储器的构建命令)
- [ ] 类型检查通过, 堆栈有一个 (`npx tsc --noEmit`, `mypy`, ...)
- [ ] 点燃过关 (存储器的提示命令)
- [ ]  新功能如预期的那样发挥作用
- [ ] 变革带有一个描述性信息

**说明:**  在更改后运行每个可能影响到它的校验命令,在成功运行后,不要重复同样的命令,除非代码自此改变——在不变的代码上重运行不会增加任何信息.

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "我会在最后测试一切" | 虫子化合物 切片1中的虫子使切片2-5错误,每个切片测试. |
| "一时一时的做起来就快了" |  这个 *感觉*  {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}直到有东西断裂 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你无法找到500条变化的线中哪条是它引起的 {\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}所以... |
| "这些变化太小了,不能单独承担" |  小承诺是自由的,大承诺是隐藏虫子,使回滚痛苦。 |
| "我待会再加特别旗" |  如果功能不完整,就不应该是用户可见的,现在就添加旗帜. |
| "这个重构小到足以包含" | 混合特性的重构使复读和调试都更加困难,将它们分开. |
| "让我再管理一个建筑指挥部 只是为了确定" | 运行成功后, 除非代码自此更改, 重复相同的命令不会添加任何内容 。 在后续编辑后再次运行, 而不是作为保证 。 |

## Red Flags


-  未进行测试而写100多行代码
- 单次递增的多个不相关的变化
- "让我快点加上这个" 扩展范围
- 跳过 test/verify 步骤以更快移动
- 建构或测试在增量之间断裂
- 累积的大型未承诺变化
-  在第三次使用之前 构建抽象
- 触摸任务范围以外的文件 "当我在这里"
-  为一次性操作创建新的工具文件
- 运行相同 build/test 连续命令两次, 没有任何干扰代码更改

## Verification


 在完成某项任务的所有递增后:

- [ ]  每一次递增都经过个别测试和检查
- [ ] 测试套房的全程通行证
- [ ] 建筑是干净的
- [ ] 特性工作按指定的端到端
- [ ]  没有未承付的变动

## 另见

增量核查是本地检查。 在宣布任务完成之前, 应用全项目定义“ Done” 作为最后闸门, 无论任务如何, 常备栏都清除加量 。 `../../references/definition-of-done.md`.
