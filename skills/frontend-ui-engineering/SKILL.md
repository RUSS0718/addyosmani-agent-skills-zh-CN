---
name: frontend-ui-engineering
description: Builds production-quality, accessible, responsive user-facing UIs. Use when building or modifying interfaces and pages, creating components, implementing layouts, meeting WCAG accessibility requirements, managing state, or when the output needs to look and feel production-quality rather than AI-generated.
---

#  前端 : UI 工程学

## Overview


建立生产质量的用户界面,这些界面可以访问、表演和视光。 UI  这看起来像是由顶级公司的设计意识工程师建造的 — — 不像是由AI生成的。 这意味着真正的设计系统坚持性、适当的可访问性、深思熟虑的互动模式,以及没有通用的“AI美学 ” 。

## When to Use


- 新建 UI 组件或页面
- 修改现有的用户界面
- 实施响应性布局
- 增加互动或国家管理
- 修复视觉或 UX 问题

## 构件结构

###  文件结构

将所有与某个部件有关的东西都凝结起来:

```
src/components/
  TaskList/
    TaskList.tsx          # Component implementation
    TaskList.test.tsx     # Tests
    TaskList.stories.tsx  # Storybook stories (if using)
    use-task-list.ts      # Custom hook (if complex state)
    types.ts              # Component-specific types (if needed)
```

### 组件图案

**将组成置于配置之上:**

```tsx
// Good: Composable
<Card>
  <CardHeader>
    <CardTitle>Tasks</CardTitle>
  </CardHeader>
  <CardBody>
    <TaskList tasks={tasks} />
  </CardBody>
</Card>

// Avoid: Over-configured
<Card
  title="Tasks"
  headerVariant="large"
  bodyPadding="md"
  content={<TaskList tasks={tasks} />}
/>
```

**保持组件的焦点 :**

```tsx
// Good: Does one thing
export function TaskItem({ task, onToggle, onDelete }: TaskItemProps) {
  return (
    <li className="flex items-center gap-3 p-3">
      <Checkbox checked={task.done} onChange={() => onToggle(task.id)} />
      <span className={task.done ? 'line-through text-muted' : ''}>{task.title}</span>
      <Button variant="ghost" size="sm" onClick={() => onDelete(task.id)}>
        <TrashIcon />
      </Button>
    </li>
  );
}
```

** 从演示文稿中获取的单独数据:**

```tsx
// Container: handles data
export function TaskListContainer() {
  const { tasks, isLoading, error, refetch } = useTasks();

  if (isLoading) return <TaskListSkeleton />;
  if (error) return <ErrorState message="Failed to load tasks" retry={refetch} />;
  if (tasks.length === 0) return <EmptyState message="No tasks yet" />;

  return <TaskList tasks={tasks} />;
}

// Presentation: handles rendering
export function TaskList({ tasks }: { tasks: Task[] }) {
  return (
    <ul role="list" className="divide-y">
      {tasks.map(task => <TaskItem key={task.id} task={task} />)}
    </ul>
  );
}
```

## 州管理层

**选择最简单的可行方法 :**

```
Local state (useState)           → Component-specific UI state
Lifted state                     → Shared between 2-3 sibling components
Context                          → Theme, auth, locale (read-heavy, write-rare)
URL state (searchParams)         → Filters, pagination, shareable UI state
Server state (React Query, SWR)  → Remote data with caching
Global store (Zustand, Redux)    → Complex client state shared app-wide
```

**避免钻孔深度超过3级.**  如果通过不使用道具的组件传递道具,则引入上下文或重组组件树.

## 设计系统遵守

### 参考资料 UI 质量

 当产品需要明确视觉方向时,在选择布局前收集证据:

1. 搜索可信赖的参考目录或使用产品组提供的参考文献.
2. 研究两三个相关的屏幕。记录关于层次、密度、导航、控制、反应行为和相互作用状态的决定。
3.  在实施之前将这些决定变成一份短的设计合同。 指定屏幕的工作、主要行动、要求的状态、反应性规则和模式来拒绝。
4.  在产品本身的组件、标志、内容和视觉语言中重建有用的结构。 永远不要复制另一产品的品牌、专有文本、图像或精确的布局。

使用引用作为证据,而不是模板。如果无法引用,请根据产品的现有设计系统记录假设并核实结果。

### 避免AI美学

AI 生成 UI 具有可识别的图案。避免所有图案 :

| AI 默认 |  为什么是个问题 | 生产质量 |
|---|---|---|
| Purple/indigo  所有的东西 | 模型默认为视觉“ 安全” 调色板, 使每个应用程序看起来都完全相同 | 使用工程的实际调色板 |
| 梯度过大 | 渐变增加视觉噪声,并与大多数设计系统发生冲突 | 与设计系统相匹配的平缓或微妙的梯度 |
|  四舍五入了所有东西 (圆形-2xl) | 最大圆形信号“ 友好” , 但忽略了在真实设计中角光度的等级 | 设计系统产生的连续边界辐射 |
| 通用英雄区 | 模板驱动的布局与实际内容或用户需要无关 | 内容第一布局 |
| Lorem 自然样式副本 | 占位符文本隐藏真实内容显示的布局问题 (长度、 包装、 溢出) | 真实的占位符内容 |
| 到处都是超大小的铺垫 | 同等慷慨的垫板会破坏视觉结构 浪费屏幕空间 | 一致的间隔尺度 |
| 股票牌网格 | 统一网格是忽略信息优先级和扫描模式的布局快捷键 | 目的驱动布局 |
| 阴影重度设计 | 分层阴影添加了与内容竞争的深度,并慢了低端设备的渲染 |  除非设计系统指定,否则暗影或无暗影 |

### 间距和布局

使用一致的间距尺度。 不要创建值 :

```css
/* Use the scale: 0.25rem increments (or whatever the project uses) */
/* Good */  padding: 1rem;      /* 16px */
/* Good */  gap: 0.75rem;       /* 12px */
/* Bad */   padding: 13px;      /* Not on any scale */
/* Bad */   margin-top: 2.3rem; /* Not on any scale */
```

### 类型

尊重类型等级 :

```
h1 → Page title (one per page)
h2 → Section title
h3 → Subsection title
body → Default text
small → Secondary/helper text
```

 不要跳过标题级别, 不要使用标题样式来表示非标题内容 。

### 颜色

- 使用语义色彩符号 : `text-primary`, `bg-surface`, `border-default` ——不是原始六进制值
-  确保充分的对比性 (4.5:1 普通文本,3:1 大文本)
-  不要只靠颜色来传递信息 (使用图标、文本或图案)

## 无障碍 (WCAG 2.1 AA)

 每个组成部分必须符合这些标准:

### 键盘导航

```tsx
// Every interactive element must be keyboard accessible
<button onClick={handleClick}>Click me</button>        // ✓ Focusable by default
<div onClick={handleClick}>Click me</div>               // ✗ Not focusable
<div role="button" tabIndex={0} onClick={handleClick}    // ✓ But prefer <button>
     onKeyDown={e => {
       if (e.key === 'Enter') handleClick();
       if (e.key === ' ') e.preventDefault();
     }}
     onKeyUp={e => {
       if (e.key === ' ') handleClick();
     }}>
  Click me
</div>
```

### ARIA 标签

```tsx
// Label interactive elements that lack visible text
<button aria-label="Close dialog"><XIcon /></button>

// Label form inputs
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// Or use aria-label when no visible label exists
<input aria-label="Search tasks" type="search" />
```

### 重点管理

```tsx
// Move focus when content changes
function Dialog({ isOpen, onClose }: DialogProps) {
  const closeRef = useRef<HTMLButtonElement>(null);

  useEffect(() => {
    if (isOpen) closeRef.current?.focus();
  }, [isOpen]);

  // Trap focus inside dialog when open
  return (
    <dialog open={isOpen}>
      <button ref={closeRef} onClick={onClose}>Close</button>
      {/* dialog content */}
    </dialog>
  );
}
```

### 有意义的空和错误状态

```tsx
// Don't show blank screens
function TaskList({ tasks }: { tasks: Task[] }) {
  if (tasks.length === 0) {
    return (
      <div role="status" className="text-center py-12">
        <TasksEmptyIcon className="mx-auto h-12 w-12 text-muted" />
        <h3 className="mt-2 text-sm font-medium">No tasks</h3>
        <p className="mt-1 text-sm text-muted">Get started by creating a new task.</p>
        <Button className="mt-4" onClick={onCreateTask}>Create Task</Button>
      </div>
    );
  }

  return <ul role="list">...</ul>;
}
```

## 响应设计

移动设计,然后扩展:

```tsx
// Tailwind: mobile-first responsive
<div className="
  grid grid-cols-1      /* Mobile: single column */
  sm:grid-cols-2        /* Small: 2 columns */
  lg:grid-cols-3        /* Large: 3 columns */
  gap-4
">
```

 在这些断点试验:320px,768px,1024px,1440px.

## 装入和过渡

```tsx
// Skeleton loading (not spinners for content)
function TaskListSkeleton() {
  return (
    <div className="space-y-3" aria-busy="true" aria-label="Loading tasks">
      {Array.from({ length: 3 }).map((_, i) => (
        <div key={i} className="h-12 bg-muted animate-pulse rounded" />
      ))}
    </div>
  );
}

// Optimistic updates for perceived speed
function useToggleTask() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: toggleTask,
    onMutate: async (taskId) => {
      await queryClient.cancelQueries({ queryKey: ['tasks'] });
      const previous = queryClient.getQueryData(['tasks']);

      queryClient.setQueryData(['tasks'], (old: Task[]) =>
        old.map(t => t.id === taskId ? { ...t, done: !t.done } : t)
      );

      return { previous };
    },
    onError: (_err, _taskId, context) => {
      queryClient.setQueryData(['tasks'], context?.previous);
    },
  });
}
```

## 另见

详细无障碍要求和测试工具见 `../../references/accessibility-checklist.md`.

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "无障碍性是不错的" |  这是许多法域的法律要求和工程质量标准. |
| "我们以后会让它反应迅速" | 改造后的应变设计比从开始建造要难3x. |
| "设计不是最终的,所以我会跳过造型" | 使用设计系统默认值 。 UI 给审查员造成破碎的第一印象。 |
| "这只是个原型" | 原型成为生产代码,建立基础右侧. |
| "现在的AI美学还不错" |  它信号质量低,从一开始就使用项目的实际设计系统. |

## Red Flags


- 超过200行的部件 (拆开他们)
- 内嵌样式或任意像素值
- 缺少错误状态、 装入状态或空状态
-  没有键盘导航测试
- 颜色作为状态的唯一指标 (red/green  没有文本或图标)
- 通用的“ AI 外观 ” (紫色梯度、超大小的牌、股票布局)

## Verification


建成后 UI:

- [ ] 组件不发生控制台错误
- [ ]  所有交互式元素都可以访问键盘 (标签通过页面)
- [ ] 屏幕阅读器可以传递页面的内容和结构
- [ ] 响应:工作在320px,768px,1024px,1440px
- [ ] 酌情处理装入、空置、错误、成功和权限状态
- [ ] 遵循项目的设计系统 (间距、 颜色、 打印)
- [ ] 得出的结果是最终结果 UI- 具体的结束门审查
- [ ] dev 工具或轴芯中没有访问警告
