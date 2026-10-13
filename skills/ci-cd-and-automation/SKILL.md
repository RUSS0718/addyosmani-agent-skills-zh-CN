---
name: ci-cd-and-automation
description: Automates CI/CD pipeline setup. Use when setting up or modifying build and deployment pipelines. Use when you need to automate quality gates, configure test runners in CI, or establish deployment strategies.
---

# CI/CD 和自动化

## Overview


将质量门自动化,以便不经过测试、加注、打字检查和建造,任何变化都无法达到生产。 CI/CD  是所有其他技能的执法机制——它捕捉到人类和特工所错过的东西,而且它始终如一地在每一个变化中这样做。

**向左移动 :** 尽可能早地在输油管中发现问题。 滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴。 滴滴滴滴滴滴滴滴滴滴滴滴滴的滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴 滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴滴吐 滴滴滴滴滴滴滴滴滴滴滴滴滴 滴滴滴滴滴滴滴滴 滴滴滴滴滴滴滴滴滴滴滴 滴滴滴滴滴滴滴滴滴滴滴滴滴 滴滴滴滴滴滴 滴 滴滴滴滴 滴滴滴滴滴 滴滴滴滴滴滴 滴滴滴滴滴 滴 滴滴 滴 滴滴滴滴滴 滴滴滴滴 滴 滴 滴滴 滴滴滴 滴

**更快比较安全 :** 更小批量和更频繁的释放会降低风险,而不是增加风险。 3个变化的部署比30个变化的部署更容易调试。 频繁释放会增强对释放过程本身的信心。

## When to Use


- 设立新项目 CI 输油管
- 添加或修改自动检查
- 配置部署管道
- 何时应启动自动核查
- 调试 CI 失败

## 质量门管道

 每个变化在合并前都会穿过这些门:

```
Pull Request Opened
    │
    ▼
┌─────────────────┐
│   LINT CHECK     │  eslint, prettier
│   ↓ pass         │
│   TYPE CHECK     │  tsc --noEmit
│   ↓ pass         │
│   UNIT TESTS     │  jest/vitest
│   ↓ pass         │
│   BUILD          │  npm run build
│   ↓ pass         │
│   INTEGRATION    │  API/DB tests
│   ↓ pass         │
│   E2E (optional) │  Playwright/Cypress
│   ↓ pass         │
│   SECURITY AUDIT │  npm audit
│   ↓ pass         │
│   BUNDLE SIZE    │  bundlesize check
└─────────────────┘
    │
    ▼
  Ready for review
```

**任何门都无法跳过 。**  如果 lint 失败, 修补 lint —— 不要禁用规则 。 如果测试失败, 修补代码 —— 不要跳过测试 。

## GitHub 动作配置

### 基础 CI 管道

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npx tsc --noEmit

      - name: Test
        run: npm test -- --coverage

      - name: Build
        run: npm run build

      - name: Security audit
        run: npm audit --audit-level=high
```

### 带有数据库集成测试

```yaml
  integration:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: testdb
          POSTGRES_USER: ci_user
          POSTGRES_PASSWORD: ${{ secrets.CI_DB_PASSWORD }}
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'npm'
      - run: npm ci
      - name: Run migrations
        run: npx prisma migrate deploy
        env:
          DATABASE_URL: postgresql://ci_user:${{ secrets.CI_DB_PASSWORD }}@localhost:5432/testdb
      - name: Integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgresql://ci_user:${{ secrets.CI_DB_PASSWORD }}@localhost:5432/testdb
```

> **说明:** 即使是为了 CI- 仅测试数据库,使用 GitHub 证书秘诀而非硬编码值,这营造了良好的习惯,防止在其他情况下意外重复使用测试证书。

### E2E 测试

```yaml
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'npm'
      - run: npm ci
      - name: Install Playwright
        run: npx playwright install --with-deps chromium
      - name: Build
        run: npm run build
      - name: Run E2E tests
        run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

## 进食 CI 失败返回代理商

权力 CI 使用 AI 代理是反馈循环 。 CI 失败 :

```
CI fails
    │
    ▼
Copy the failure output
    │
    ▼
Feed it to the agent:
"The CI pipeline failed with this error:
[paste specific error]
Fix the issue and verify locally before pushing again."
    │
    ▼
Agent fixes → pushes → CI runs again
```

**关键模式 :**

```
Lint failure → Agent runs `npm run lint --fix` and commits
Type error  → Agent reads the error location and fixes the type
Test failure → Agent follows debugging-and-error-recovery skill
Build error → Agent checks config and dependencies
```

## 部署战略

### 预览部署

 每个 PR 都得到一个预览部署, 用于手动测试 :

```yaml
# Deploy preview on PR (Vercel/Netlify/etc.)
deploy-preview:
  runs-on: ubuntu-latest
  if: github.event_name == 'pull_request'
  steps:
    - uses: actions/checkout@v4
    - name: Deploy preview
      run: npx vercel --token=${{ secrets.VERCEL_TOKEN }}
```

### 特性旗

特性旗从发布中解析部署。 在旗帜后面部署不完整或有风险的特征, 这样您可以:

- **船代号不能允许** 提前合并到主机, 准备后启用 。
- ** 不调兵而退.** 禁用旗帜而不是返回代码 。
- ** 加那利的新特征。** 启用1%的用户,然后是10%,然后是100%。
- **运行 A/B 测试。** 将行为与特征和不特征进行对比.

```typescript
// Simple feature flag pattern
if (featureFlags.isEnabled('new-checkout-flow', { userId })) {
  return renderNewCheckout();
}
return renderLegacyCheckout();
```

**旗帜生命周期 :** 创建 / 启用测试 / 加那利 / 全面推出 / 删除国旗和死亡代码。 永久存在的旗帜成为技术债务 —— 在创建时设定清理日期 。

### 分阶段推出

```
PR merged to main
    │
    ▼
  Staging deployment (auto)
    │ Manual verification
    ▼
  Production deployment (manual trigger or auto after staging)
    │
    ▼
  Monitor for errors (15-minute window)
    │
    ├── Errors detected → Rollback
    └── Clean → Done
```

### 退缩计划

 每次部署均应可逆转:

```yaml
# Manual rollback workflow
name: Rollback
on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Version to rollback to'
        required: true

jobs:
  rollback:
    runs-on: ubuntu-latest
    steps:
      - name: Rollback deployment
        run: |
          # Deploy the specified previous version
          npx vercel rollback ${{ inputs.version }}
```

## 环境管理

```
.env.example       → Committed (template for developers)
.env                → NOT committed (local development)
.env.test           → Committed (test environment, no real secrets)
CI secrets          → Stored in GitHub Secrets / vault
Production secrets  → Stored in deployment platform / vault
```

CI 不应该有生产秘密。 CI 测试。

## 外侧自动化 CI

### 依赖/翻新

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: npm
    directory: /
    schedule:
      interval: weekly
    open-pull-requests-limit: 5
```

### 构建警察角色

指定一个人负责保存 CI 绿色。当建筑中断时,建筑警察的工作是修复或恢复,而不是改变造成中断的人。这可以防止建筑中断的积累,而每个人都假设其他人会修复。

### 公关检查

- **要求的审查:** 合并前至少1项批准
- **需要的状态检查 :** CI 合并前必须先通过
- **部门保护:**  没有强力弹出主力
- **自动合并 :**  如果所有检查都通过并获得批准,则自动合并

## CI 优化

 当输油管超过10分钟时,按照撞击的先后次序应用这些策略:

```
Slow CI pipeline?
├── Cache dependencies
│   └── Use actions/cache or setup-node cache option for node_modules
├── Run jobs in parallel
│   └── Split lint, typecheck, test, build into separate parallel jobs
├── Only run what changed
│   └── Use path filters to skip unrelated jobs (e.g., skip e2e for docs-only PRs)
├── Use matrix builds
│   └── Shard test suites across multiple runners
├── Optimize the test suite
│   └── Remove slow tests from the critical path, run them on a schedule instead
└── Use larger runners
    └── GitHub-hosted larger runners or self-hosted for CPU-heavy builds
```

**示例:缓存和平行**
```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npm run lint

  typecheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npx tsc --noEmit

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npm test -- --coverage
```

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "CI "太慢了" "太慢了" | 优化管道 ( 见 CI  以下优化) 不要跳过 5分钟的管道可以防止调试时数 |
| "这个变化是微不足道的,跳过 CI" |  三维变化断层构建 。 CI 快速的微小变化反正。 |
| "测试是片状的,只是重新运行" | 虚伪的测试掩盖了真实的虫子 浪费每个人的时间 修复虚伪 |
| "我们将添加 CI 以后再说吧 |  无项目 CI 积聚破碎的状态,在第一天建立 |
| "手工测试就够了" | 手动测试没有规模,而且无法重复。 自动操作您所能做的 。 |

## Red Flags


-  没有 CI  项目中
- CI 失败被忽略或沉默
- 禁用的测试 CI 使管道通过
- 未经中转核查就部署生产
-  无回滚机制
- 隐藏在代码或密码中的秘密 CI 配置文件 ( 不是秘密管理员)
- 长 CI 时间,没有优化努力

## Verification


设置或修改后 CI:

- [ ]  所有质量的门都存在 (插图、类型、测试、构建、审计)
- [ ] 管道运行在每一个 PR 并推向主
- [ ] 失败块合并 (已配置分支保护)
- [ ] CI 成果反馈到发展循环中
- [ ] 秘密存储在机密管理器里,而不是密码里
- [ ] 部署有一个回滚机制
- [ ] 管道在10分钟内运行 测试套房
