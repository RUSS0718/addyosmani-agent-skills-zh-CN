---
name: deprecation-and-migration
description: Manages deprecation and migration. Use when removing old systems, APIs, or features. Use when migrating users from one implementation to another. Use when migrating a database schema in production, such as renaming or dropping a column without downtime (expand/contract). Use when deciding whether to maintain or sunset existing code.
---

# 折旧和移徙

## Overview


代码是一种责任,而不是资产. 每行代码都有持续的维护成本——修复错误,更新依赖,应用安全补丁,以及上任的新工程师. 折旧是去除不再获得其保存的代码的纪律,迁移是将用户安全地从旧到新的过程.

 大多数工程组织都擅长建筑东西,很少有人擅长拆除这些东西。这种技巧解决了这一差距。

## When to Use


- 取代旧制度, API,或带有新文本的库
- 日出一个不再需要的特征
- 合并重复执行
- 移除无人拥有的死亡代码,但每个人都依赖于
- 规划新系统的生命周期 (设计时开始折旧规划)
-  决定是维持遗留系统还是投资于移徙

## 核心原则

### 代码是责任

 每行代码都有持续的成本:它需要测试,文档,安全补丁,依赖性更新,以及任何在附近工作的人的精神管理费用。代码的价值是它所提供的功能,而不是代码本身。当同样的功能可以得到更少的代码,更少的复杂,或者更好的抽象时——旧代码应该去.

### Hyrum's Law 使删除变得困难

有了足够多的用户,每个可观察到的行为都变得依赖——包括bug,time quirks,以及没有记录的副作用. 这就是为什么贬低需要主动迁移,而不仅仅是宣布. 用户在依赖替换不复制的行为时不能"只是切换".

### 设计时间的折旧规划启动

 当建造新东西时,问道:"我们怎样在3年内去除这个东西?" 设计有干净接口,特征旗帜,最小表面积的系统比向各地泄露执行细节的系统更容易贬值.

## B. 折旧决定

 在贬低任何东西之前,请回答这些问题:

```
1. Does this system still provide unique value?
   → If yes, maintain it. If no, proceed.

2. How many users/consumers depend on it?
   → Quantify the migration scope.

3. Does a replacement exist?
   → If no, build the replacement first. Don't deprecate without an alternative.

4. What's the migration cost for each consumer?
   → If trivially automated, do it. If manual and high-effort, weigh against maintenance cost.

5. What's the ongoing maintenance cost of NOT deprecating?
   → Security risk, engineer time, opportunity cost of complexity.
```

## 强制对咨询折旧

| 类型 | 何时使用 | 机制 |
|------|-------------|-----------|
| **咨询** | 移徙是可选的,旧制度是稳定的 | 警告、 文档、 粘贴。 用户在自己的时间范围内迁移 。 |
| **义务** | 旧系统有安全问题、街区进展或维护费用无法持续 | 硬期限 旧系统将按日期X删除 提供迁移工具. |

**默认为咨询。** 只有在维护成本或风险证明强制移民是正当的时才强制使用。 强制贬值需要提供移民工具、文件和支持 — — 你不能仅仅宣布一个最后期限。

## 移徙进程

### 步骤1:建立替换机制

 没有工作替代品,不要贬值。

- 涵盖旧系统的所有关键用途案例
- 拥有证件和移民指南
-  在生产中证明 (不只是"理论上更好")

### 步骤2:宣布和文件

```markdown
## Deprecation Notice: OldService

**Status:** Deprecated as of 2025-03-01
**Replacement:** NewService (see migration guide below)
**Removal date:** Advisory — no hard deadline yet
**Reason:** OldService requires manual scaling and lacks observability.
            NewService handles both automatically.

### Migration Guide
1. Replace `import { client } from 'old-service'` with `import { client } from 'new-service'`
2. Update configuration (see examples below)
3. Run the migration verification script: `npx migrate-check`
```

### 步骤3:逐步迁移

一次一个地迁移消费者,而不是一次一次。

```
1. Identify all touchpoints with the deprecated system
2. Update to use the replacement
3. Verify behavior matches (tests, integration checks)
4. Remove references to the old system
5. Confirm no regressions
```

**楚恩规则:**  如果你拥有被贬值的基础设施,你负责迁移你的用户——或者提供不需要迁移的反向兼容的更新。不要宣布贬值,让用户去弄清楚。

###  第4步:删除旧系统

只有在所有消费者迁移之后:

```
1. Verify zero active usage (metrics, logs, dependency analysis)
2. Remove the code
3. Remove associated tests, documentation, and configuration
4. Remove the deprecation notices
5. Celebrate — removing code is an achievement
```

## 移徙模式

### Stangler 模式

同步运行新旧系统。 路线交通从新旧逐渐增加。 当旧系统处理0%的交通时, 取消它 。

```
Phase 1: New system handles 0%, old handles 100%
Phase 2: New system handles 10% (canary)
Phase 3: New system handles 50%
Phase 4: New system handles 100%, old system idle
Phase 5: Remove old system
```

### 适配器模式

创建一个适配器,将旧界面的调用翻译为新执行。消费者在移动后端时继续使用旧界面。

```typescript
// Adapter: old interface, new implementation
class LegacyTaskService implements OldTaskAPI {
  constructor(private newService: NewTaskService) {}

  // Old method signature, delegates to new implementation
  getTask(id: number): OldTask {
    const task = this.newService.findById(String(id));
    return this.toOldFormat(task);
  }
}
```

### 特性旗移

使用特性旗将用户一次从旧系统切换到新系统 :

```typescript
function getTaskService(userId: string): TaskService {
  if (featureFlags.isEnabled('new-task-service', { userId })) {
    return new NewTaskService();
  }
  return new LegacyTaskService();
}
```

### 数据库 Schema 迁移 (Expand/Contract)

变换计划是最危险的迁移, 因为数据是无法通过返回部署而回滚的。 失败模式是将变换计划合并到代码更改中: 在同一版中重新命名一列, 开始使用新名称, 在推出窗口中—— 当新旧代码同时运行时, 其中之一正在查询一个不存在的列。 定点是 **永远不要更改列的位置**. 在添加剂阶段的迁移,如此新旧的代码在每一个步骤上都是有效的。

```
EXPAND ──────────────→ MIGRATE ──────────────→ CONTRACT
add the new column,    backfill existing rows,  once no code reads the
nullable, alongside    dual-write old+new from  old column, drop it in
the old one            the app                  a later, separate deploy
```

**工作实例——重命名 `name` 改为: `full_name`:**

1. **扩展。** 添加 `full_name`  是无效的 部署 (旧代码忽略了它;没有破解.)
2. **双写.** App 双写 `name` 财务报告和财务报告 `full_name`  每页 insert/update. 部署
3. **后补.** 复制 `name → full_name` 用于现有的行,分批进行,所以不锁定表格。
4. **切换读取。** 指向应用程序 `full_name`继续写 部署和烘焙
5. **合同。** 停止写作 `name`,然后——在一个 *分开, 稍后* 部署——放下纵队。

 每个步骤都是可独立部署和可逆转的:如果第4步行为不当,则将代码回滚和 `full_name` 将每个阶段视为一个细长的垂直切片——见 `incremental-implementation` 技术。

**规则:**
- ** 先加点,再加点,再加点,再加点,再加点** 添加数 (新建无效列、 新表格、 新索引)  在任何部署中安全; 投放和重命名得到自己的部署 *之后*  不引用旧形状的代码 。
- ** 每一次迁移都有一条经过考验的道路。** 无法逆转的迁移是无法回转的部署。 `down`  在合并之前。
- **分批回填,脱离热路.** 单人 `UPDATE` 超过数百万行锁住桌子, 块块和油门。
- ** 在不阻断写的情况下构建大索引** ( 例如,邮递服务 `CREATE INDEX CONCURRENTLY`).
- ** 按特性旗从代码中解析**  当切换有风险时, 恰好与上面的地物旗迁移模式相同。

## 僵尸代码

Zombie代码是没有人拥有但每个人都依赖的代码,它没有积极维护,没有明确的拥有者,并积累安全漏洞和兼容性问题. 标志:

- 6个月以上没有犯罪,但有活跃的消费者
-  没有指定维护者或团队
- 失败的测试,没有人修复
- 已知弱点的依赖性,无人更新
- 参考已不存在的系统的文件

**答复:** 僵尸代码不能停留在边缘 — — 它要么得到投资,要么被清除。

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "它仍然有效,为什么去除它?" |  没有人坚持的工作守则会积累安全债务和复杂性。 维护成本无声增长。 |
| "以后有人会需要它" |  如果以后需要,可以重建. 保留未使用的代码"以防万一"比重建花费更多. |
| "移民太贵了" | 将移民成本与持续2-3年的维护成本相比较,移民通常更便宜的长期成本。 |
| "等我们完成新制度后,再拆掉它" | 折旧规划从设计时间开始,到新系统完成时,你将有新的优先级,现在就计划. |
| "用户会独自迁徙" |  不会的,提供工具,文件和奖励,或者自己做移民 (楚恩规则). |
| "我们可以无限期地维持两个系统" |  两个系统做同样的事情,是维护、测试、记录和登机成本的两倍。 |
| "只是重命名专栏,这是一行" |  在推出期间,新旧代码一起运行——人们会查询一个已经不存在的列. Expand/contract中,永远不要重新命名。 |
| "我再加柱子,把旧的丢在同一迁移" |  那对夫妇是破坏力下降的一个安全因素。在代码未提及旧形状后,滴水会得到自己的部署。 |
| "如果需要我们会写回卷" |  无向下路径的迁移是无法逆向的部署。 写入并运行 `down`  在合并之前。 |

## Red Flags


- 已折旧但无可用替代品的系统
-  没有移徙工具或证件的折旧通知
- "柔软"贬值 多年来一直没有进展
-  没有所有者和活跃消费者的僵尸代码
- 向贬值的系统添加的新功能 (投资替换)
-  不测量当前使用情况的折旧
-  在不验证零活动消费者的情况下删除代码
- 以同一部署中运出它为条件的更改和代码
- 改名或降职的栏目,而不是通过 expand/contract
- 迁移合并后没有向下路径测试, 或返回填充后锁定表格

## Verification


完成折旧后:

- [ ] 更换是生产证明,涵盖所有关键用途案例
- [ ] 存在带有具体步骤和实例的移徙指南
- [ ]  所有活跃的消费者都已迁移 ( 经核查 metrics/logs)
- [ ] 旧代码、测试、文档和配置被完全删除
- [ ] 密码库中不再提及已贬值的系统
- [ ] 折旧通知被删除 ( 他们已经达到目的了)

 经过数据库的迁移:

- [ ] 处于添加剂阶段的改变船 (合同),没有在位置上编辑
- [ ]  新旧密码对每个部署步骤的策略都有效
- [ ]  每次迁移都有经过测试的下行路径;回填以节奏批次运行
- [ ] 破坏性步骤 (drop/rename)  在未引用旧形状代码后, 舰只自行部署
