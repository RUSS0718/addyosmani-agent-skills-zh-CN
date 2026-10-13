---
name: code-simplification
description: Simplifies code for clarity. Use when refactoring code for clarity without changing behavior. Use when code works but is harder to read, maintain, or extend than it should be. Use when reviewing code that has accumulated unnecessary complexity.
---

# 代码简化

> 被人们所鼓舞 [Claude Code 简化插件](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/code-simplifier/agents/code-simplifier.md)。在这里适应为任何AI编码代理的模型不可知性,过程驱动技能.

## Overview


简化代码在保留准确行为的同时降低复杂度. 目标不是更少的行——这是更容易读取,理解,修改和调试的代码,每次简化都必须通过一个简单的测试:"新队员理解这个速度会比原作快吗?"

## When to Use


- 完成一个功能,测试通过, 但执行感觉比它需要的更重
-  在代码审查期间,可读性或复杂性问题被标出
-  当遇到深层的逻辑、长函数或不明名称时
-  在时间压力下重构代码时
- 合并分散在文件之间的相关逻辑时
- 合并产生重复或不一致的改动后

** 不使用时 :**

- 代码已经是干净和可读的——不要为了它而简化
-  你不明白代码是干什么的 在你简化之前先明白
- 代码对性能很关键,"简单"的版本要慢得多
-  你即将完全重写模块——简化丢弃代码的废物处理工作.

## 五项原则

### 1. 保护行为

 不要改变代码的作用——只是它如何表达它。所有输入,输出,副作用,错误行为,和边缘大小写必须保持不变。如果你不确定简化会保留行为,就不要做到。

```
ASK BEFORE EVERY CHANGE:
→ Does this produce the same output for every input?
→ Does this maintain the same error behavior?
→ Does this preserve the same side effects and ordering?
→ Do all existing tests still pass without modification?
```

### 2. 遵循项目公约

简化意味着使代码与代码库更加一致,而不是强加外部偏好。在简化前:

```
1. Read CLAUDE.md / project conventions
2. Study how neighboring code handles similar patterns
3. Match the project's style for:
   - Import ordering and module system
   - Function declaration style
   - Naming conventions
   - Error handling patterns
   - Type annotation depth
```

简化断裂项目一致性不是简化——只是简单化.

### 3. 聪明才智更突出

 当紧凑版本需要精神暂停来解析时,明确代码比紧凑代码要好.

```typescript
// UNCLEAR: Dense ternary chain
const label = isNew ? 'New' : isUpdated ? 'Updated' : isArchived ? 'Archived' : 'Active';

// CLEAR: Readable mapping
function getStatusLabel(item: Item): string {
  if (item.isNew) return 'New';
  if (item.isUpdated) return 'Updated';
  if (item.isArchived) return 'Archived';
  return 'Active';
}
```

```typescript
// UNCLEAR: Chained reduces with inline logic
const result = items.reduce((acc, item) => ({
  ...acc,
  [item.id]: { ...acc[item.id], count: (acc[item.id]?.count ?? 0) + 1 }
}), {});

// CLEAR: Named intermediate step
const countById = new Map<string, number>();
for (const item of items) {
  countById.set(item.id, (countById.get(item.id) ?? 0) + 1);
}
```

### 4. 保持平衡

简化有一个失败模式: 过度简化。 注意这些陷阱 :

- **内衬太猛烈了** ——删除一个给一个概念一个名字的助手,使得呼叫站点更难读取.
- **组合不相关的逻辑** ——两个简单的功能合并为一个复杂的功能并不简单.
- ** 删除“ 不必要的” 抽象** - 存在一些关于可扩展性或可检验性的抽象,而不是复杂性
- **优化行数** - 减少线条不是目标;更容易理解。

### 5. 改变什么的范围

默认简化最近修改的代码。 除非明确要求拓宽范围, 否则避免不相关的代码的驱动器重构。 未扩展的简化会在 diff 中产生噪音, 并有意外回归的风险 。

## 简化进程

### 步骤1:在触摸前先了解 (Chesterton's Fence)

 在更改或删除任何东西之前,请了解它为什么存在。 Chesterton's Fence:如果你看到横过一条道路的栅栏,却不明白它为什么会存在,就不要拆掉它,首先了解原因,然后决定原因是否仍然适用.

```
BEFORE SIMPLIFYING, ANSWER:
- What is this code's responsibility?
- What calls it? What does it call?
- What are the edge cases and error paths?
- Are there tests that define the expected behavior?
- Why might it have been written this way? (Performance? Platform constraint? Historical reason?)
- Check git blame: what was the original context for this code?
```

 如果你不能回答这些,你还没有准备好简化,先读更多上下文.

### 步骤2:查明简化的机会

扫描这些图案——每个图案都是具体的信号,而不是模糊的气味:

**结构复杂性:**

|  图案 | 信号 | 简化 |
|---------|--------|----------------|
| 深层筑巢 (3+级) | 难以跟踪控制流 | 将条件提取到警卫条款或助手职能中 |
| 长期职能 (50+线) | 多重责任 | 分割为带有描述性名称的焦点功能 |
| 嵌入式三角形 | 需要智能堆栈来解析 | 替换为 if/else 链条、切换或查找对象 |
| 布尔参数旗 | `doThing(true, false, true)` | 替换为选项对象或单独的函数 |
| 重复条件 | 一样 `if` 检查多个位置 | 提取到名副其实的上游函数 |

**命名和可读性:**

|  图案 | 信号 | 简化 |
|---------|--------|----------------|
| 通用名称 | `data`, `result`, `temp`, `val`, `item` | 重命名以描述内容 : `userProfile`, `validationErrors` |
| 缩略名称 | `usr`, `cfg`, `btn`, `evt` |  除非缩写是通用的,否则使用完整的单词 (`id`, `url`, `api`) |
| 错误名称 | 函数名称 `get` 也可以变异状态 | 重命名以反映实际行为 |
| 解释“什么”的评论 | `// increment counter` 上一级 `count++` |  删除注释——代码足够清晰. |
| 解释“为什么”的评论 | `// Retry because the API is flaky under load` | 留着这些 密码不能表达的意向 |

**冗余时间:**

|  图案 | 信号 | 简化 |
|---------|--------|----------------|
| 重复的逻辑 | 多个位置的相同 5+ 线 | 提取到共享函数 |
| 死代码 | 无法到达的分支, 未使用的变量, 注释区块 |  删除 ( 在确认它真的死了之后) |
| 不必要的抽象 |  无值的折叠器 | 内置包装, 直接调用基本函数 |
| 过度工程模式 | 工厂为工厂,战略为一战略 | 以简单的直接办法取代 |
| 重复类型的断言 | 铸造到一种已经推断出来的类型 |  删除此断言 |

### 步骤 3: 逐步应用更改

一次简化一次,每次更改后进行测试。 **将更改与特性或错误修正分开提交 。** 重构和添加一个特征的公关是两个公关——拆分它们.

```
FOR EACH SIMPLIFICATION:
1. Make the change
2. Run the test suite
3. If tests pass → commit (or continue to next simplification)
4. If tests fail → revert and reconsider
```

避免将多个简化组合成一个单一的未经测试的更改。如果有问题,需要知道是哪个简化导致的。

**规则500:**  如果重构会触及500多条线路,则投资自动化 (代码、 脚本、 AST 转换) 手动编辑这个比例尺容易出错,而且很累赘。

### 步骤 4: 验证结果

 在经过种种简化后,退后一步,评价整个过程:

```
COMPARE BEFORE AND AFTER:
- Is the simplified version genuinely easier to understand?
- Did you introduce any new patterns inconsistent with the codebase?
- Is the diff clean and reviewable?
- Would a teammate approve this change?
```

 如果“简化”版本更难理解或审查,请返回。并非所有简化尝试都成功。

## 语言特定指导

### TypeScript / JavaScript

```typescript
// SIMPLIFY: Unnecessary async wrapper
// Before
async function getUser(id: string): Promise<User> {
  return await userService.findById(id);
}
// After
function getUser(id: string): Promise<User> {
  return userService.findById(id);
}

// SIMPLIFY: Verbose conditional assignment
// Before
let displayName: string;
if (user.nickname) {
  displayName = user.nickname;
} else {
  displayName = user.fullName;
}
// After
const displayName = user.nickname || user.fullName;

// SIMPLIFY: Manual array building
// Before
const activeUsers: User[] = [];
for (const user of users) {
  if (user.isActive) {
    activeUsers.push(user);
  }
}
// After
const activeUsers = users.filter((user) => user.isActive);

// SIMPLIFY: Redundant boolean return
// Before
function isValid(input: string): boolean {
  if (input.length > 0 && input.length < 100) {
    return true;
  }
  return false;
}
// After
function isValid(input: string): boolean {
  return input.length > 0 && input.length < 100;
}
```

### Python

```python
# SIMPLIFY: Verbose dictionary building
# Before
result = {}
for item in items:
    result[item.id] = item.name
# After
result = {item.id: item.name for item in items}

# SIMPLIFY: Nested conditionals with early return
# Before
def process(data):
    if data is not None:
        if data.is_valid():
            if data.has_permission():
                return do_work(data)
            else:
                raise PermissionError("No permission")
        else:
            raise ValueError("Invalid data")
    else:
        raise TypeError("Data is None")
# After
def process(data):
    if data is None:
        raise TypeError("Data is None")
    if not data.is_valid():
        raise ValueError("Invalid data")
    if not data.has_permission():
        raise PermissionError("No permission")
    return do_work(data)
```

### React /联署材料X

```tsx
// SIMPLIFY: Verbose conditional rendering
// Before
function UserBadge({ user }: Props) {
  if (user.isAdmin) {
    return <Badge variant="admin">Admin</Badge>;
  } else {
    return <Badge variant="default">User</Badge>;
  }
}
// After
function UserBadge({ user }: Props) {
  const variant = user.isAdmin ? 'admin' : 'default';
  const label = user.isAdmin ? 'Admin' : 'User';
  return <Badge variant={variant}>{label}</Badge>;
}

// SIMPLIFY: Prop drilling through intermediate components
// Before — consider whether context or composition solves this better.
// This is a judgment call — flag it, don't auto-refactor.
```

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "它有用,不用碰它" | 难以读取的工作代码在破解时会很难修正,现在简化可以节省未来每次更改的时间. |
| "Fewer的台词总是比较简单" | 1号线的嵌入式三胞胎不比5号线简单 if/else. 简单化是指理解速度,而不是线条计数. |
| "我也会很快简化这个无关的代码" |  未瞄准的简化在代码中制造了噪音和风险回归。 保持专注 。 |
| "类型使得它自我文件化" | 类型文档结构,而非意图。一个名符其实的函数解释 * 为什么*  比类型签名更好的解释 *怎么样?*. |
| "这个抽象可能以后有用" |  不要保留投机性抽象,如果现在没有使用,就是复杂而无价值,需要时删除并重新添加. |
| "原作者一定是有原因的" | 可能吧 请检查date=中的日期值 Chesterton's Fence 但是,积累的复杂性往往没有任何理由;它只是压力下迭代的残余. |
| "我会在增加这个功能的同时重构" | 与特性工作分开重构。 混合变化在历史中更难审查、回归和理解。 |

## Red Flags


- 简化需要修改测试才能通过 ( 你可能改变行为)
- "简化"的代码比原来的要长,更难遵循
- 重新命名以适应你的喜好 而不是项目常规
- 清除错误处理, 因为“ 它使代码更清洁 ”
- 简化你不明白的代码
- 将许多简化变成一个难以审查的庞大任务
- 在当前任务范围之外重构代码而不被询问

## Verification


填写简化通行证后:

- [ ]  所有现有测试通过,不作修改
- [ ] 构建成功但没有新的警告
- [ ] Linter/formatter 传球 ( 无样式回归)
- [ ]  每次简化都是可审查的渐进变化
- [ ] diff是干净的,没有不相关的变化混合在
- [ ] 简化的代码遵循项目惯例 (对照CLAUDE.md或同等文件进行核对)
- [ ]  没有删除或削弱任何错误处理
- [ ]  没有留下死亡密码 ( 未用进口,无法进入的分支)
- [ ] 团队成员或审查代理人将核准这一变动,作为净改进
