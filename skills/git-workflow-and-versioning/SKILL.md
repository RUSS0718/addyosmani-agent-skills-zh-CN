---
name: git-workflow-and-versioning
description: Structures git workflow practices. Use when making any code change. Use when committing, branching, resolving conflicts, splitting uncommitted work in a messy working tree into clean atomic commits, opening or reviewing a pull request (PR), pushing to a remote, or when you need to organize work across multiple parallel streams. Use when cutting a release, choosing a semantic version bump, tagging, or writing a changelog.
---

# Git 工作流程和版本

## Overview


Git  您的安全网。 将承诺作为保存点, 将分支作为沙箱, 将历史作为文档。 由于 AI 代理以高速生成代码, 规范的版本控制是使修改可以管理、 可审查、 可逆的机制 。

## When to Use


永远,每个代码的更改都通过git来进行.

## 核心原则

###  基于中子的发展 ( 建议)

保留 `main`  在短命的特征分支中工作,这些分支在1-3天内合并。长寿的发展分支是隐藏的成本 — — 它们存在分歧,造成合并冲突,并拖延整合。 DORA研究始终显示基于干线的开发与高性能的工程团队相关。

```
main ──●──●──●──●──●──●──●──●──●──  (always deployable)
        ╲      ╱  ╲    ╱
         ●──●─╱    ●──╱    ← short-lived feature branches (1-3 days)
```

 这是推荐的默认值。 使用 gitflow 或长寿命分支的团队可以调整原理 。 (原子承诺、小变化、描述性信息)  他们的分支模式——承诺的纪律比具体的分支战略更重要。

- **发展分支是成本。** 树枝每天生活,会累积风险。
- **释放分支是可以接受的.**  当你需要稳定一个释放 同时主前进。
- **特色旗 > 长枝.** 最好是在旗帜后面部署不完整的工作,而不是把工作放在分支上几个星期。

### 1. 提前提交,经常提交

 每一次成功的增量都会得到自己的承诺,不要累积大量没有承诺的改变.

```
Work pattern:
  Implement slice → Test → Verify → Commit → Next slice

Not this:
  Implement everything → Hope it works → Giant commit
```

提交是保存点。 如果下一次更改中断, 您可以立即恢复到最后一个已知的好状态 。

### 2. 原子能委员会

每一个承诺都有一条逻辑:

```
# Good: Each commit is self-contained
git log --oneline
a1b2c3d Add task creation endpoint with validation
d4e5f6g Add task creation form component
h7i8j9k Connect form to API and add loading state
m1n2o3p Add task creation tests (unit + integration)

# Bad: Everything mixed together
git log --oneline
x1y2z3a Add task feature, fix sidebar, update deps, refactor utils
```

### 3. 说明性信息

提交信件解释 * 为什么*,不仅仅是 *怎么样?*:

```
# Good: Explains intent
feat: add email validation to registration endpoint

Prevents invalid email formats from reaching the database.
Uses Zod schema validation at the route handler level,
consistent with existing validation patterns in auth.ts.

# Bad: Describes what's obvious from the diff
update auth.ts
```

**格式 :**
```
<type>: <short description>

<optional body explaining why, not what>
```

**类型 :**
- `feat` ——新特色.
- `fix` - 错误修复
- `refactor` ——代码更改,既不修补错误,也不添加特性
- `test` - 增加或更新测试
- `docs` ——仅限文件
- `chore` - 工具、依赖、配置

### 4. 将关切分开

 不要将格式化的改变和行为变化结合起来。 不要将功能化的重构件结合起来。 每种类型的改变都应该是单独的承诺—— 最好是单独的 PR:

```
# Good: Separate concerns
git commit -m "refactor: extract validation logic to shared utility"
git commit -m "feat: add phone number validation to registration"

# Bad: Mixed concerns
git commit -m "refactor validation and add phone number field"
```

**与特性工作分离重构.** 重构变化和特征变化是两个不同的变化——分别提交。这使得每项变化都更容易在历史中审查、回归和理解。小的清理 (重命名变量) 可由审查者斟酌决定,将内容纳入一个特征。

### 5. 变化大小

 每个目标~100行 commit/PR. 超过 ~ 1 000 行的更改应拆分 。 请参见 拆分策略 。 `code-review-and-quality` 如何打破大的变化。

```
~100 lines  → Easy to review, easy to revert
~300 lines  → Acceptable for a single logical change
~1000 lines → Split into smaller changes
```

## 分支战略

### 特征处

```
main (always deployable)
  │
  ├── feature/task-creation    ← One feature per branch
  ├── feature/user-settings    ← Parallel work
  └── fix/duplicate-tasks      ← Bug fixes
```

- 分行从 `main` (或团队默认分支)
- 保持树枝短寿命 (1-3天内合并) ——长寿树枝是隐性成本.
- 合并后删除分支
- 较长寿命的分支更偏爱不完全的特征

### 分支命名

```
feature/<short-description>   → feature/task-creation
fix/<short-description>       → fix/duplicate-tasks
chore/<short-description>     → chore/update-deps
refactor/<short-description>  → refactor/auth-module
```

## 与工作树合作

 对于平行的AI代理工作,使用git worktrees同时运行多个分支:

```bash
# Create a worktree for a feature branch
git worktree add ../project-feature-a feature/task-creation
git worktree add ../project-feature-b feature/user-settings

# Each worktree is a separate directory with its own branch
# Agents can work in parallel without interfering
ls ../
  project/              ← main branch
  project-feature-a/    ← task-creation branch
  project-feature-b/    ← user-settings branch

# When done, merge and clean up
git worktree remove ../project-feature-a
```

福利:
- 多个特工可以同时研究不同的特性
- 不需要切换分支 ( 每个目录都有自己的分支)
-  如果一个实验失败, 删除工作树—— 没有任何损失
-  在明确合并之前, 更改被隔离

## 保存点模式

```
Agent starts work
    │
    ├── Makes a change
    │   ├── Test passes? → Commit → Continue
    │   └── Test fails? → Revert to last commit → Investigate
    │
    ├── Makes another change
    │   ├── Test passes? → Commit → Continue
    │   └── Test fails? → Revert to last commit → Investigate
    │
    └── Feature complete → All commits form a clean history
```

 这种模式意味着你永远不会失去一个以上的增量工作。如果一个特工离开铁路, `git reset --hard HEAD` 带你回到最后一个成功的州。

## 更改摘要

 经过任何修改后,请提供一份结构化摘要。这使审查、文件范围纪律和表露出意想不到的变化变得更容易:

```
CHANGES MADE:
- src/routes/tasks.ts: Added validation middleware to POST endpoint
- src/lib/validation.ts: Added TaskCreateSchema using Zod

THINGS I DIDN'T TOUCH (intentionally):
- src/routes/auth.ts: Has similar validation gap but out of scope
- src/middleware/error.ts: Error format could be improved (separate task)

POTENTIAL CONCERNS:
- The Zod schema is strict — rejects extra fields. Confirm this is desired.
- Added zod as a dependency (72KB gzipped) — already in package.json
```

 这种模式提前捕获错误的假设,并给审查者一个清晰的改变图. "DIDN'TOUCH"部分特别重要——它显示你行使了范围纪律,没有进行未经请求的翻新.

## 承诺前卫生

 在每个人承诺之前:

```bash
# 1. Check what you're about to commit
git diff --staged

# 2. Ensure no secrets
git diff --staged | grep -i "password\|secret\|api_key\|token"

# 3. Run tests
npm test

# 4. Run linting
npm run lint

# 5. Run type checking
npx tsc --noEmit
```

 用 git 钩子自动化 :

```json
// package.json (using lint-staged + husky)
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

## 处理生成的文件

- **提交生成的文件** 只有在项目需要时 ( 例如, `package-lock.json`Prisma 迁移)
- ** 别犯法** 构建输出 (`dist/`, `.next/`),环境文件 (`.env`),或 IDE 配置 (`.vscode/settings.json`  除非共享)
- **来,来,来,来 `.gitignore`** 涵盖: `node_modules/`, `dist/`, `.env`, `.env.local`, `*.pem`

## 使用( E) Git 用于调试

```bash
# Find which commit introduced a bug
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>
# Git checkouts midpoints; run your test at each to narrow down

# View what changed recently
git log --oneline -20
git diff HEAD~5..HEAD -- src/

# Find who last changed a specific line
git blame src/services/task.ts

# Search commit messages for a keyword
git log --grep="validation" --oneline
```

## 发布版本( V)

交代是怎样的 *说吧* 音轨变化; a **版本** 怎么样的你 *消费者* 跟踪它。其他的一切都取决于您的代码——另一个团队,一个公布的软件包,一个部署的客户端——“最新主机”不再足以回答“我运行的是什么,升级安全吗?”一个版本的号码和一个更改日志是应答它的合同。

### 语义版本

 对于任何与消费者,版本 `MAJOR.MINOR.PATCH` 并让数字带有以下含义:

```
  MAJOR  breaking change — consumers must change their code to upgrade
  MINOR  new functionality, backward-compatible — safe to upgrade
  PATCH  bug fix, backward-compatible — safe to upgrade
```

数字是一种承诺,所以让代码与之匹配。一个改变消费者所依赖的行为的“patch”是一件大变化,戴着伪装 (Hyrum's Law - 看见了 `api-and-interface-design` 技能) 当不确定变化是否正在破裂时,假设是;惊喜主机远比崩溃的消费者便宜。

### 标记释放, 让标记成为真相的来源

释放是历史上一个不可改变的点,不是移动的分支。标记它,这样它就永远可以被复制:

```bash
git tag -a v1.4.0 -m "Release 1.4.0"
git push origin v1.4.0
```

将版本从标签中衍生出来,而不是在分散的文件中进行手工编辑,所以文物,标签,以及更改日志都永远不会有异议.

###  为人类保留一个更改日志

更改日志不是 `git log` 这是被整理的 消费者的回答 “什么改变了,我在乎吗?” `Added / Changed / Fixed / Deprecated / Removed / Security`上面是最新的,每个条目的词 围绕着用户的撞击,而不是内部的力学。

```markdown
## [1.4.0] - 2025-06-12
### Added
- Bulk task import via CSV
### Fixed
- Timezone drift in recurring task due dates
### Deprecated
- `GET /v1/tasks/all` — use the paginated `GET /v1/tasks` (removal in 2.0)
```

将条目写入进行更改的相同更改中, 而撞击是新鲜的 —— 不是在发布时从承诺的考古学中重建的 。 断裂的更改会得到迁移注释和折叠窗口 。 (跟着 `deprecation-and-migration` 技能); 实际释放是: `shipping-and-launch` 技能的工作——本节是提供它的版本合同.

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "我将承诺,当特写完成" |  一个巨大的任务无法检讨、调试或返回。 提交每个切片 。 |
| "消息不关你的事" | 信件是文档 。 (和未来代理人) 需要了解什么是变化和原因。 |
| "我待会就把它压碎" | 争吵摧毁了发展叙事,从一开始就倾向于干净的增量承诺。 |
| "砖块加高空" | 短寿命的分支是自由的,防止工作冲突碰撞. 长寿命的分支是问题所在——在1-3天内合并. |
| "我待会再分这个零钱" | 较大变化更难审查,更难部署,也更难恢复。在提交前分头处理,而不是在提交后分头处理。 |
| "我不需要一个. 吉提格诺尔" | 一直持续到 `.env`  有制作机密,马上安排好 |
| "这只是一个小的修补, 撞上补丁" | 检查一下消费者能观察到什么。他们所依赖的改变行为是一件大事,无论大小大小。 |
| "变更日志只是承诺日志" | 责任是属于你的,而变更日志是属于消费者的,通过撞击来解释。从原始责任中产生一个责任。 |
| "我们会在发布时写下更改日志" |  到时撞击会从内存中重建, 并且有一半缺失。 用更改写入条目 。 |

## Red Flags


- 累积的大型未承诺变化
- 提交“ 修补 ” 、 “ 更新 ” 、 “ Misc” 等信件
- 将改变与行为变化混合格式化
-  没有 `.gitignore`  在项目中
- 提交 `node_modules/`, `.env`,或者建造文物
- 长寿的树枝与主要树枝相差很大
- 强制到共享分支
- 在一个小版本或补丁版本的凸起下运来的断开更改
-  没有标签的发布, 或版本编号与标签同步后手动编辑
-  没有更改日志条目的用户信息发布, 或只是丢弃的更改日志传输消息

## Verification


 对于每一项承诺:

- [ ] 交代有一件事合乎逻辑
- [ ] 信息解释为什么,遵循公约类型
- [ ] 试验通过后,才能实施
- [ ] diff里没有秘密
- [ ]  没有仅格式化的修改与行为变化混合
- [ ] `.gitignore` 涵盖标准除外情形

 每一次释放 (任何与消费者有关的东西):

- [ ] 版本的凸起与变化相符: 破 × 主要, 添加剂 → 小, 修补 → 补丁
- [ ] 发行版被标记,版本来自标签,而不是手编辑出同步
- [ ] 更改日志中包含一个可读的可读条目,
