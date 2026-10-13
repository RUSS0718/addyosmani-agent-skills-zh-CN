# 测试模式参考 (JavaScript/TypeScript)

快速引用时间 JavaScript/TypeScript 测试模式—— Jest, React 测试库,超级测试,以及 Playwright ——从中说明普遍原则. `test-driven-development` 原则 (安排代理代理、命名、模拟纪律、反间谍) 应用在任何生态系统中; 这里显示的语法和工具是 JS/TS-specific.  在另一堆中,用寄存器自己的测试框架和命令遵循同样的原理.

##  目录

- [测试结构 (安排代理助理)](#test-structure-arrange-act-assert)
- [测试命名公约](#test-naming-conventions)
- [常见请求](#common-assertions)
- [模拟模式](#mocking-patterns)
- [React/组件测试](#reactcomponent-testing)
- [API / 集成测试](#api--integration-testing)
- [E2E 测试 (Playwright)](#e2e-testing-playwright)
- [试验反恐怖分子](#test-anti-patterns)

## 测试结构 (安排代理助理)

```typescript
it('describes expected behavior', () => {
  // Arrange: Set up test data and preconditions
  const input = { title: 'Test Task', priority: 'high' };

  // Act: Perform the action being tested
  const result = createTask(input);

  // Assert: Verify the outcome
  expect(result.title).toBe('Test Task');
  expect(result.priority).toBe('high');
  expect(result.status).toBe('pending');
});
```

## 测试命名公约

```typescript
// Pattern: [unit] [expected behavior] [condition]
describe('TaskService.createTask', () => {
  it('creates a task with default pending status', () => {});
  it('throws ValidationError when title is empty', () => {});
  it('trims whitespace from title', () => {});
  it('generates a unique ID for each task', () => {});
});
```

## 常见请求

```typescript
// Equality
expect(result).toBe(expected);           // Strict equality (===)
expect(result).toEqual(expected);        // Deep equality (objects/arrays)
expect(result).toStrictEqual(expected);  // Deep equality + type matching

// Truthiness
expect(result).toBeTruthy();
expect(result).toBeFalsy();
expect(result).toBeNull();
expect(result).toBeDefined();
expect(result).toBeUndefined();

// Numbers
expect(result).toBeGreaterThan(5);
expect(result).toBeLessThanOrEqual(10);
expect(result).toBeCloseTo(0.3, 5);      // Floating point

// Strings
expect(result).toMatch(/pattern/);
expect(result).toContain('substring');

// Arrays / Objects
expect(array).toContain(item);
expect(array).toHaveLength(3);
expect(object).toHaveProperty('key', 'value');

// Errors
expect(() => fn()).toThrow();
expect(() => fn()).toThrow(ValidationError);
expect(() => fn()).toThrow('specific message');

// Async
await expect(asyncFn()).resolves.toBe(value);
await expect(asyncFn()).rejects.toThrow(Error);
```

## 模拟模式

### 模拟函数

```typescript
const mockFn = jest.fn();
mockFn.mockReturnValue(42);
mockFn.mockResolvedValue({ data: 'test' });
mockFn.mockImplementation((x) => x * 2);

expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith('arg1', 'arg2');
expect(mockFn).toHaveBeenCalledTimes(3);
```

### 模拟模块

```typescript
// Mock an entire module
jest.mock('./database', () => ({
  query: jest.fn().mockResolvedValue([{ id: 1, title: 'Test' }]),
}));

// Mock specific exports
jest.mock('./utils', () => ({
  ...jest.requireActual('./utils'),
  generateId: jest.fn().mockReturnValue('test-id'),
}));
```

### 仅锁定边界

```
Mock these:                    Don't mock these:
├── Database calls             ├── Internal utility functions
├── HTTP requests              ├── Business logic
├── File system operations     ├── Data transformations
├── External API calls         ├── Validation functions
└── Time/Date (when needed)    └── Pure functions
```

## React/组件测试

```tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';

describe('TaskForm', () => {
  it('submits the form with entered data', async () => {
    const onSubmit = jest.fn();
    render(<TaskForm onSubmit={onSubmit} />);

    // Find elements by accessible role/label (not test IDs)
    await screen.findByRole('textbox', { name: /title/i });
    fireEvent.change(screen.getByRole('textbox', { name: /title/i }), {
      target: { value: 'New Task' },
    });
    fireEvent.click(screen.getByRole('button', { name: /create/i }));

    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({ title: 'New Task' });
    });
  });

  it('shows validation error for empty title', async () => {
    render(<TaskForm onSubmit={jest.fn()} />);

    fireEvent.click(screen.getByRole('button', { name: /create/i }));

    expect(await screen.findByText(/title is required/i)).toBeInTheDocument();
  });
});
```

## API / 集成测试

```typescript
import request from 'supertest';
import { app } from '../src/app';

describe('POST /api/tasks', () => {
  it('creates a task and returns 201', async () => {
    const response = await request(app)
      .post('/api/tasks')
      .send({ title: 'Test Task' })
      .set('Authorization', `Bearer ${testToken}`)
      .expect(201);

    expect(response.body).toMatchObject({
      id: expect.any(String),
      title: 'Test Task',
      status: 'pending',
    });
  });

  it('returns 422 for invalid input', async () => {
    const response = await request(app)
      .post('/api/tasks')
      .send({ title: '' })
      .set('Authorization', `Bearer ${testToken}`)
      .expect(422);

    expect(response.body.error.code).toBe('VALIDATION_ERROR');
  });

  it('returns 401 without authentication', async () => {
    await request(app)
      .post('/api/tasks')
      .send({ title: 'Test' })
      .expect(401);
  });
});
```

## E2E 测试 (Playwright)

```typescript
import { test, expect } from '@playwright/test';

test('user can create and complete a task', async ({ page }) => {
  // Navigate and authenticate
  await page.goto('/');
  await page.getByRole('textbox', { name: /email/i }).fill('test@example.com');
  await page.getByLabel(/password/i).fill('testpass123');
  await page.getByRole('button', { name: /log in/i }).click();

  // Create a task
  await page.getByRole('button', { name: /new task/i }).click();
  await page.getByRole('textbox', { name: /title/i }).fill('Buy groceries');
  await page.getByRole('button', { name: /create/i }).click();

  // Verify task appears
  const task = page.getByRole('listitem', { name: /buy groceries/i });
  await expect(task).toBeVisible();

  // Complete the task
  await task.getByRole('checkbox', { name: /complete buy groceries/i }).check();
  await expect(task).toHaveCSS('text-decoration-line', 'line-through');
});
```

## 试验反恐怖分子

| 反治疗者 | 问题 | 更好的办法 |
|---|---|---|
| 测试实施细节 | 重构符断开 | 测试 inputs/outputs |
|  快拍下所有东西 |  没有人审查快照 diffs | 指定特定值 |
| 共享可变状态 | 试验互相污染 | Setup/teardown  每一次测试 |
| 测试第三方代码 | 浪费时间,不是你的虫子 | 锁定边界 |
| 跳过测试才能通过 CI | 隐藏真正的错误 | 修补或删除测试 |
| 使用( E) `test.skip` 永久 | 死代码 |  删除或修补 |
| 过于宽泛的说法 | 无法抓住回归 | 具体 |
|  没有同步错误处理 | 错误和假通行证 | 总是 `await` Aync 测试 |
