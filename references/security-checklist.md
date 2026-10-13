# 安全检查清单

快速的网络应用程序安全引用。同时使用 `security-and-hardening` 技术。

##  目录

- [威胁模型 ( 从这里开始)](#threat-modeling-start-here)
- [承诺前检查](#pre-commit-checks)
- [认证](#authentication)
- [授权](#authorization)
- [输入验证](#input-validation)
- [安全信头](#security-headers)
- [CORS 配置](#cors-configuration)
- [数据保护](#data-protection)
- [依赖性](#dependency-security)
- [AI / LLM 安全](#ai--llm-security)
- [处理错误](#error-handling)
- [OWASP  前 10 个快速引用](#owasp-top-10-quick-reference)
- [OWASP LLMs 快速引用前十名](#owasp-top-10-for-llms-quick-reference)

## 威胁模型 ( 从这里开始)

 在达到控制前,要花5分钟 思考像攻击者:

- [ ] 绘制信任边界 (请求、上传、webhooks、第三方 API、 LLM 输出,以及您不控制的进程所写的本地值)
- [ ] 资产名称 (证书、PII、付款数据、管理行动、货币流动)
- [ ]  每个边界的STRIDE运行 (偷窥、放纵、放逐、信息披露、DoS、电梯)
- [ ]  在使用案件旁边写明的虐待案件 ("我怎么会滥用这个?")

## 承诺前检查

- [ ] 密码里没有秘密 (`git diff --cached | grep -i "password\|secret\|api_key\|token"`)
- [ ] `.gitignore` 涵盖: `.env`, `.env.local`, `*.pem`, `*.key`
- [ ] `.env.example` 使用占位符值 ( 不是真实的秘密)

## 认证

- [ ]  用 bcrypt 拼接的密码 ( * 12发),按键,或 ⁇ 2
- [ ]  会话 cookie : `httpOnly`, `secure`, `sameSite: 'lax'`
- [ ]  会话过期已配置 (合理最大年龄)
- [ ]  对登录端点的速率限制 ( 每15分钟10次尝试)
- [ ] 密码重置符号: 有时间限制 (1小时)单次使用
- [ ] 重复失败后账户关闭 (选项,带有通知)
- [ ]  为敏感行动提供支助的外交部 ( 可选但建议)

## 授权

- [ ]  每个受保护的端点检查认证
- [ ]  每一次资源访问检查 ownership/role (防止 IDOR 代码)
- [ ] 管理端点需要管理角色核查
- [ ] API 范围为最小必要权限的密钥
- [ ] 验证 JWT 令牌 (签名、到期、发行人)

## 输入验证

- [ ]  在系统边界验证所有用户输入 (API 路径, 窗体处理器)
- [ ] 验证使用许可列表 ( 非拒绝列表)
- [ ] 字符串长度受限 (min/max)
- [ ] 已验证的数值范围
- [ ] 电子邮件 URL,并用适当的图书馆验证日期格式
- [ ]  文件上传: 类型限制, 大小限制, 内容核实
- [ ] SQL 查询参数化 ( 无字符串连接)
- [ ] HTML 输出编码 (使用框架自动逃避)
- [ ] 重定向前已验证的 URL (防止打开方向调整)
- [ ] 服务器侧 URL 允许的获取; private/reserved IP 被屏蔽 (防止SSRF系统)
- [ ] 破坏路径操作 (delete/move/overwrite): 符号链接已解析, 允许列出根, 最小深度, 在调用前读取所有权证据

### 破坏性路径操作

 对由数据命名的目标进行封装。首先决定,然后决定,并处理
结果是一名候选人,而不是作为授权:

```typescript
import { realpath, readFile } from 'node:fs/promises';
import { resolve, relative, isAbsolute, join, sep } from 'node:path';

const ALLOWED_ROOTS = ['/var/lib/myapp/sessions']; // an allowlist, not a pattern
const MIN_DEPTH = 1;                               // so a root is never the target

async function resolveDeletable(candidate: string, expectedOwner: string) {
  const target = await realpath(resolve(candidate)); // symlinks resolved BEFORE the check
  const inRoot = ALLOWED_ROOTS.some((root) => {
    const rel = relative(root, target);
    // `rel === '..'` / `'../'` only — a plain `startsWith('..')` would also
    // reject a legitimate child named `..cache`.
    if (rel === '' || rel === '..' || rel.startsWith(`..${sep}`) || isAbsolute(rel)) return false;
    return rel.split(sep).length >= MIN_DEPTH;
  });
  if (!inRoot) throw new Error(`refusing: outside allowed roots (${target})`);

  const owner = await readFile(join(target, '.owner'), 'utf8').catch(() => null);
  if (owner?.trim() !== expectedOwner) throw new Error(`refusing: unproven owner (${target})`);
  return target;
}
```

 这不起作用,必须说明片段是从哪里复制的:

- **标记是自证.** 任何可以在根内写的东西都可以写
  `.owner`. `expectedOwner`  必须来自认证状态, 标记需要
  廉洁保护 (限制性所有权,或MAC) 未经批准
  而不是对错误产生的目标进行一致性检查。
- **返回一条路会留下一条 check/use 种族。**  如果一个不可信进程可以互换
   在支票和电话之间的祖先, 操作在描述器上,没有跟随,
  根深蒂固的语义论,或保证等级论在持续期间是不可改变的.

## 安全信头

```
Content-Security-Policy: default-src 'self'; script-src 'self'
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 0  (disabled, rely on CSP)
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

## CORS 配置

```typescript
// Restrictive (recommended)
cors({
  origin: ['https://yourdomain.com', 'https://app.yourdomain.com'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
})

// NEVER use in production:
cors({ origin: '*' })  // Allows any origin
```

## 数据保护

- [ ] 敏感领域,不包括在 API 答复 (`passwordHash`, `resetToken`, 等 (简体中文).)
- [ ]  未登录的敏感数据 (密码、 代号、 完整 CC 号码)
- [ ] PII 在休息时加密 ( 如果条例要求,)
- [ ] HTTPS 用于所有外部通信
- [ ] 加密数据库备份
- [ ] 个人数据被分类、按照既定目的收集并尽量减少
- [ ] 个人数据有保留限制和工作删除路径 (包括备份、缓存、索引)
- [ ] Export/delete (数据主体) 必要时支持请求;第三方共享有同意和数据处理协议

## 依赖性

 第一个位置是 **安装边界**。如果软件包与父软件包匹配 `workspaces` ,使用该工作空间根;否则使用拥有其显示和依赖图的最近的工程根。在该边界上,验证 `packageManager` ( 当存在时),锁文件,和 CI 命令。如果存在不一致或相互竞争的管理器锁定文件,则停止。一个嵌入式项目只有在母工作区之外才能独立;独立的子项目可以合法地使用不同的管理器。

| Manager/version 信号 | Frozen/immutable CI 安装 | 已知咨询审计 |
|---|---|---|
| npm (`package-lock.json` 或 `npm-shrinkwrap.json`) | `npm ci` | `npm audit` |
| pnpm | `pnpm install --frozen-lockfile` | `pnpm audit` |
| 亚伦 2+ | `yarn install --immutable` | `yarn npm audit -A -R` |
| 亚伦1号 | `yarn install --frozen-lockfile` | `yarn audit` |

 对于未上市的管理器或版本,请查看其官方文档;不替换另一个管理器的命令或较新的默认.

### 安装脚本门

永远不要通过在未验证默认的客户端上首次执行普通安装来发现依赖性生命周期脚本.

1. 禁用依赖脚本的 Bootstrap, 或有文件记录的默认拒绝政策加失败关闭执行 。
2.  在批准前检查准确的脚本来源和包版本.
3. 记录最窄的本地 allow/deny  在安装边界上执行政策,并做出承诺。
4. 运行干净 frozen/immutable 安装此政策,并核实所需的软件包仍在建设中。

**即时快照 :** 软件包管理器默认和命令名快速更改 。 在依赖它之前, 要先根据绑定客户端当前的官方文档验证此矩阵 。

| 管理器版本 | 土著政策 |
|---|---|
| npm 未经核实的颗粒式核准 | 带靴子的 `npm ci --ignore-scripts`,或持续 `ignore-scripts=true`  当计划设置全工程屏蔽时。在允许任何已审查的依赖脚本之前,保留脚本禁用或有意升级。 |
| npm 11.18.x (韩语). (11.1.8. 经核实的) |  未审查的依赖脚本在默认情况下带有警告。 执行 `strict-allow-scripts=true`  在正常安装之前,先使用工作空间- unaware `npm install-scripts ls`  从安装边界; 保持批准版本的标记和全名的否定。 |
| npm 12.x 电话 (12.0.1号核实) |  未审查的依赖脚本默认跳过; `strict-allow-scripts=true` 使其存在在执行前失败安装 。 使用相同的 `npm install-scripts` 审查和批准流程。 |
| pnpm 11+ | 使用( U) `pnpm approve-builds` 承诺 `allowBuilds` (c) 决定; `strictDepBuilds` 默认为 `true`,所以未经审查的建筑失败。 |
| pnpm 10.26-10.x (中文(简体) ). | 配置 `allowBuilds` 明示,或使用 `pnpm approve-builds` 与遗产 `onlyBuiltDependencies` / `ignoredBuiltDependencies` 列表。设置 `strictDepBuilds: true`;其 v10 默认值为 `false`. |
| pnpm 10.1–10.25 | `pnpm approve-builds` 记录遗留列表; 启用 `strictDepBuilds` 得到支持的地方 (10.3+). |
| 旧的或未知的 pnpm | 带靴子的 `pnpm install --frozen-lockfile --ignore-scripts`。继续禁用脚本,除非被绑定的版本文档是可执行的政策。 |
| 亚伦 4.14+ | 依赖性后置器默认已禁用 。 仅给予顶级例外 。 `dependenciesMeta.<package>.built: true`. |
| 亚伦 2–4.13 | 设定 `enableScripts: false` 输入 `.yarnrc.yml`,然后只给予最高层的例外。 `dependenciesMeta.<package>.built: true`; 不在全球启用脚本。 |
| 亚伦1号 | 带靴子的 `yarn install --ignore-scripts`; 保留脚本禁用,除非在绑定客户端的有文件记录的工作流程下审查每个必要的例外。 |

授权检查 : [npm 安装标记](https://docs.npmjs.com/cli/v11/commands/npm-install-scripts/), [安装策略](https://docs.npmjs.com/cli/v11/commands/npm-install/),以及 [CLI 释放](https://github.com/npm/cli/releases); [pnpm 核准建造](https://pnpm.io/cli/approve-builds) 财务报告和财务报告 [构建设置](https://pnpm.io/settings#allowbuilds); [亚伦安保](https://yarnpkg.com/features/security) 财务报告和财务报告 [显示](https://yarnpkg.com/configuration/manifest#dependenciesMeta).

**供应链卫生** (咨询审计没有发现新的恶意套件):
- [ ] 完全是一个权威的锁定文件 。 project/workspace 根是承诺和 CI  从不重写
- [ ] Critical/high 调查结果按可达到性三进制; 推迟有其理由和审查日期
- [ ] 强制审计补救 (`npm audit fix --force` 或相当) 绝不自动; 修改 diff 和更改日志
- [ ] 书记官处 signatures/provenance  在管理者支持的地方进行验证
- [ ] 依赖性生命周期脚本在首次执行前被屏蔽,并且只能通过被绑定的管理员的本土政策批准.
- [ ]  对新的依赖性进行所有权、维护、释放年龄、来源、过渡图和定型审查

## AI / LLM 安全

 对于任何称为 LLM 的特性 (聊天员、总结员、代理员、RAG):

- [ ] 模式产出被视为不可信,从未进入 `eval`/SQL/shell/`innerHTML`/文件路径
- [ ] 假设快速注入; 代码执行权限, 而不是系统执行权限
- [ ] 保密、跨租户数据、全系统提示都保存在上下文窗口之外
- [ ] Tool/agent 范围; 破坏性或不可逆转的行动需要确认
- [ ] 调值,率,和 recursion/loop 设定的限制 (约束消费)

## 处理错误

```typescript
// Production: generic error, no internals
res.status(500).json({
  error: { code: 'INTERNAL_ERROR', message: 'Something went wrong' }
});

// NEVER in production:
res.status(500).json({
  error: err.message,
  stack: err.stack,         // Exposes internals
  query: err.sql,           // Exposes database details
});
```

## OWASP  前 10 个快速引用

| # | 脆弱性 | 预防 |
|---|---|---|
| 1 | 中断访问控制 |  对每个端点进行认证检查, 进行所有权核查 |
| 2 | 加密失败 | HTTPS强烈的散列 密码里没有秘密 |
| 3 | 注射量 | 参数化查询、输入验证 |
| 4 |  不可靠的设计 | 威胁模型、光谱驱动发展 |
| 5 | 安全配置错误 | 安全头条、最低权限、审计道具 |
| 6 | 脆弱部分 | 生态系统的依赖性审计 (`npm audit`, `pip-audit`, ...),不断更新道普,最小的道普 |
| 7 | 认证失败 | 强烈的密码, 速度限制, 会话管理 |
| 8 | 数据完整性失败 | 校验 updates/dependencies,签名的文物 |
| 9 | 日志失败 | 日志安全事件,不要记录机密 |
| 10 | SSRF( 战略支助部队) | Validate/allowlist URL , 限制出境请求 |

## OWASP LLMs 快速引用前十名

 对于具有LLM特性的应用程序,请参见: [OWASP GenAI 安全项目](https://genai.owasp.org/llm-top-10/).

| 身份证 | 风险 | 预防 |
|---|---|---|
| LLM01 数据 | 快速注射 |  不要信任系统提示为边界; 在代码中执行权限 |
| LLM02 记录片 | 敏感信息披露 | 保留 secrets/PII 退出提示; 过滤输出 |
| LLM03 数据 | 供应链 | 类似任何依赖的 Vet 模型、 数据集和插件 |
| LLM04 常规部分 | 数据和模型中毒 | 使用信任的模型源,验证完整性;审查微调和RAG数据 |
| LLM05 数据 | 不当的输出处理 | 将模型输出视为不可信; 验证、 参数化、 编码 |
| LLM06 (法语) | 机构过多 | 范围工具权限; 确认破坏性行动 |
| LLM07 常规部分: 第261页, 1.A.2.c | 系统急漏 | 假设系统提示可以泄露; 里面没有秘密 |
| LLM08 常规 | 矢量和嵌入性弱点 |  每个租户嵌入分区RAG;在索引编制前验证文档 |
| LLM09 维基文库中相关的原始文献: LLM09 | 错误信息 | 以引用方式提供地面答案; 验证关键主张; 保持人与人之间的循环 |
| 法学硕士10 |  无限制消费 | Cap 代号、 请求率和 loop/recursion 深度 |
