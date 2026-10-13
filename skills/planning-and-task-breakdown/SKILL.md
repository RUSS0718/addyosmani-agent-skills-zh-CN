---
name: planning-and-task-breakdown
description: Breaks work into ordered tasks. Use when you have a spec or clear requirements and need to break work into implementable tasks. Use when a task feels too large to start, when you need to estimate scope, or when parallel work is possible.
---

# 规划和任务分类

## Overview


将工作分解为具有明确验收标准的可核查的小型任务。 良好的任务分解是可靠完成工作的代理人与产生纠缠的混乱的代理人之间的区别。 每个任务应小到足以执行、测试和在一次有重点的会话中核实。

## When to Use


-  你有一个规格,需要把它分解成可执行的单位
- 任务太大或模糊,无法开始
- 需要在多个代理人或会议之间开展平行工作
-  你得把范围传达给人类
- 执行令并不明显

** 不使用时 :** 单文件变化具有明显的范围,或者当Spec已经包含定义明确的任务时.

## 规划进程

###  第1步: 输入计划模式

 在写入任何代码之前, 以只读模式操作 :

- 读取 spec 和相关代码库部分
- 查明现有模式和公约
- 构件之间的地图依赖性
- 备注风险和未知数

**规划时不要写代码 。** 输出是计划文档保存到 `tasks/plan.md` 和任务列表目标中记录的任务列表 (参见输出文件; 默认 `tasks/todo.md`),而不是执行。

### 步骤2:确定依赖性图

地图什么取决于什么:

```
Database schema
    │
    ├── API models/types
    │       │
    │       ├── API endpoints
    │       │       │
    │       │       └── Frontend API client
    │       │               │
    │       │               └── UI components
    │       │
    │       └── Validation logic
    │
    └── Seed data / migrations
```

执行顺序遵循依附图自下而上:先建立基础.

### 步骤3:垂直切片

而不是建立所有的数据库,然后所有的 API,然后所有 UI ——一次构建一个完整的特征路径:

**差劲 (横向切片):**
```
Task 1: Build entire database schema
Task 2: Build all API endpoints
Task 3: Build all UI components
Task 4: Connect everything
```

**不错 (垂直切换):**
```
Task 1: User can create an account (schema + API + UI for registration)
Task 2: User can log in (auth schema + API + UI for login)
Task 3: User can create a task (task schema + API + UI for creation)
Task 4: User can view task list (query + API + UI for list view)
```

 每个垂直切片都提供工作,可测试的功能.

### 步骤 4: 写任务

每项任务都遵循这一结构,无论是在降标任务列表中降落,还是作为外部跟踪器中的项目 (参见输出文件):

```markdown
## Task [N]: [Short descriptive title]

**Description:** One paragraph explaining what this task accomplishes.

**Acceptance criteria:**
- [ ] [Specific, testable condition]
- [ ] [Specific, testable condition]

**Verification:**
- [ ] Tests pass: [the repository's focused-test command]
- [ ] Build succeeds: [the repository's build command]
- [ ] Manual check: [description of what to verify]

**Dependencies:** [Task numbers this depends on, or "None"]

**Files likely touched:**
- `src/path/to/file.ts`
- `tests/path/to/test.ts`

**Estimated scope:** [Small: 1-2 files | Medium: 3-5 files | Large: 5+ files]
```

### 步骤5:秩序和检查站

安排任务,以便:

1. 依赖性得到满足 ( 先建立基础)
2. 每项任务都使系统处于工作状态
3.  每2-3次任务后都设有核查检查站
4. 高风险任务很早 (失败的快)

 在任务清单目标中加入明确的检查站:

```markdown
## Checkpoint: After Tasks 1-3
- [ ] All tests pass
- [ ] Application builds without errors
- [ ] Core user flow works end-to-end
- [ ] Review with human before proceeding
```

## 任务大小准则

| 大小 |  文件 | 范围 | 示例 |
|------|-------|-------|---------|
| ** 页:1** | 1 | 单个函数或配置变化 | 添加验证规则 |
| **S** | 1-2 |  一个组件或终点 | 添加新内容 API 结束点 |
| **M** | 3-5 |  一个特征切片 | 用户注册流程 |
| **L** | 5-8 |  多组成部分特性 |  用过滤和页码搜索 |
| **四十届** | 8+ | **太大了,再拆开点** | — |

 如果任务为 L 或 更大的, 则它应该被分割成较小的任务。 一个代理执行 S 和 M 任务最出色 。

**何时进一步完成一项任务:**
- 需要不止一次有重点的会议 (大约2+小时的代理工作)
-  您不能用 3 个或更少 的 点来描述接受标准
-  它触及两个或多个独立的子系统 ( 例如,认证和计费)
-  你发现自己在任务标题上写着"和" ( 一个标志,这是两个任务)

## 输出文件

- **计划文件:** 将实施计划保存到 `tasks/plan.md`。这总是一个减记文件——设计决定,风险,和开放式问题不能清晰地映射到单个的跟踪者问题上.
- **任务列表 :** 将每个任务记录在 **任务列表目标** (定义如下).

创建 `tasks/`  如果目录不存在。

**永远不要覆盖一个不完整的计划。** 撰写前 `tasks/plan.md` 或 `tasks/todo.md`,检查它们是否已经存在,并且仍然包含未选中的任务 :

- 正在重新规划的同样工作 (用户要求修改或延长此计划)  • 更新现有文件。
- 不同工作* **停下来问。**  未选中的任务可能是在另一个会话中构建。 不要自行删除、 覆盖或重命名已有文件; 显示冲突并让用户决定 。 ( 先完成旧计划,明确放弃,或者告诉你新计划应该去哪里).

同样的规则也适用于一个外部任务列表目标:从不批量关闭或删除另一个计划中的开放式跟踪器项目,为新的项目腾出空间.

### 任务列表目标

任务清单的目标是记录任务和检查站的地点,它的定义是一次性的,在这里;这种技能中任何其他参考都取决于它。

- **默认: 检查清单风格的标记下文件 。 `tasks/todo.md`.**  这是《公约》 `/build` 命令和其他下游工具的预期。除非项目另有说明,否则使用它。
- **外部跟踪器 :**  如果项目的代理规则 (`CLAUDE.md`, `AGENTS.md`, 等 (简体中文).) 或用户指定问题跟踪器 (譬如说. GitHub 问题,吉拉,线性, `bd`蜂窝),每个任务创建一个跟踪器项目而不是写入 `tasks/todo.md`。将步骤4的结构映射到跟踪器的字段:项目正文中的接受标准和核查步骤,通过跟踪器的连接机制的依赖性 (`bd dep add`,"blocked"等。)将第5步检查站也记录为追踪器物品,或如果追踪器没有天然等同物,则记录为计划文件中的核对表。

 当使用外部跟踪器时,请注意 `tasks/plan.md` ( 例如,“线性项目FOO中跟踪的任务”)  因此,下游步骤和今后的会话知道在哪里查看,并保留计划文件的"任务列表"部分,作为有秩序的跟踪器项目ID或链接索引,而不是重复的核对表.

## 计划文档模板

```markdown
# Implementation Plan: [Feature/Project Name]

## Overview
[One paragraph summary of what we're building]

## Architecture Decisions
- [Key decision 1 and rationale]
- [Key decision 2 and rationale]

## Task List

### Phase 1: Foundation
- [ ] Task 1: ...
- [ ] Task 2: ...

### Checkpoint: Foundation
- [ ] Tests pass, builds clean

### Phase 2: Core Features
- [ ] Task 3: ...
- [ ] Task 4: ...

### Checkpoint: Core Features
- [ ] End-to-end flow works

### Phase 3: Polish
- [ ] Task 5: ...
- [ ] Task 6: ...

### Checkpoint: Complete
- [ ] All acceptance criteria met
- [ ] Ready for review

## Risks and Mitigations
| Risk | Impact | Mitigation |
|------|--------|------------|
| [Risk] | [High/Med/Low] | [Strategy] |

## Open Questions
- [Question needing human input]
```

 当任务在外部跟踪器中存在时,将任务列表部分保留在上方,作为有命令的跟踪器项目ID或链接的索引,而不是重复的核对表.

## 平行机会

 当有多个代理或会话时:

- **安全地平行化:** 独立的特性片、测试已执行的特性、文件
- **必须是顺序 :** 数据库迁移、共享状态变化、依赖链
- **需要协调:** 共享特性 API 合同 ( 首先定义合同,然后平行)

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "我会想办法的,我走了。" |  这就是你如何结束一个纠结的混乱 和重新工作。 10分钟的计划节省了小时。 |
| "任务很明显" |  无论如何写下来。 明确的任务显示隐藏的依赖和被遗忘的边框 。 |
| "规划是高空的" | 规划是任务,没有计划的执行只是打字而已. |
| "我可以把一切都抱在脑海里" | 上下文窗口是有限的。 书面计划在会话边界和紧凑中幸存 。 |
| "旧的... `tasks/plan.md` 已经很僵硬了,我换掉它 |  未选中的任务可能是在另一个会话中构建中。 覆盖它们会破坏不存在的工作状态 。 请停止并询问 。 |

## Red Flags


-  在没有书面任务清单的情况下开始执行
- 重叠 a `tasks/plan.md` 或 `tasks/todo.md`  这些任务在不同的工作上仍然没有限制,没有要求
- 写入 `tasks/todo.md`  当项目指定了外部跟踪器时 (或分散任务)
- 说“ 执行特性” 没有接受标准的任务
- 计划中没有核查步骤
-  所有任务都是 XL 大小
- 任务之间没有检查站
-  不考虑抚养令

## Verification


 在开始执行之前,确认:

- [ ]  每个任务都有接受标准
- [ ]  每个任务都有核查步骤
- [ ] 任务依赖性被确定并正确排序
- [ ] 任务列表目标中记录了任务 (默认 `tasks/todo.md`)
- [ ]  没有明确的用户确认,任何先前存在的不完整计划都不会被覆盖
- [ ]  没有任务触及超过~5文件
- [ ]  主要阶段之间设有检查站
- [ ] 人类已经审查并批准了计划

## 另见

接受标准是每个任务,回答“我们造了正确的东西吗?” 它们坐在全项目定义的顶端,即常设栏杆上,每件任务都比完成的好。 `../../references/definition-of-done.md`.
