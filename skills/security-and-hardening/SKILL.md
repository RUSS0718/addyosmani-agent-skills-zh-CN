---
name: security-and-hardening
description: Hardens code against vulnerabilities. Use when auditing an input handler for vulnerabilities, when handling user input, authentication, data storage, or external integrations, or when checking a login flow is safe against the OWASP Top Ten. Use when building any feature that accepts untrusted data, manages user sessions, or interacts with third-party services. Use when auditing dependencies for known vulnerabilities, triaging package-manager audit findings, or assessing supply-chain risk in a new package. Use when personal data or privacy compliance (GDPR, CCPA) is involved.
---

# 安保和硬化

## Overview


网络应用的安全第一开发实践。将每个外部输入视为敌意,每个秘密视为神圣,每一个授权检查都视为强制性。安全不是一个阶段——它制约着每一个触及用户数据,认证,或外部系统的代码线.

## When to Use


- 构建任何接受用户输入的内容
- 执行认证或授权
- 储存或传送敏感数据
- 与外部API或服务整合
- 正在添加文件上传、 webhooks 或回调
- 处理付款或PII数据

## 进程:威胁模式第一

 没有威胁模型的控制器被锁定是猜测的。在硬化之前,要像攻击者一样花5分钟思考:

1. **绘制信任边界。** 无人信任的数据会进入你的系统吗? HTTP 请求、窗体字段、文件上传、webhooks、第三方 API、信件队列以及 **LLM 输出** 外加本地值, 因为 OS 把它们交给您而看起来是内部的 : 另一个进程的命令行或环境, 共享音量的文件名, 工作有效载荷中的路径 。 trust follows who *写* a 值,不是哪个信道传送的。每个边界都是攻击的表面。
2. **说出资产。**  有什么值钱的呢?
3. **在每个边界上运行 STRIDE** - 快速镜头,不是仪式:

| 威胁 | 询问 | 典型的缓解 |
|---|---|---|
| **S**偷窥 | 能不能有人冒充一个 user/service? | 认证、签名核查 |
| **T**饱和 | 数据能否在过境时或休息时改变? | 廉政检查,参数化查询, HTTPS |
| **R**休 学 | 以后能否拒绝采取行动? | 安全事件的审计记录 |
| **I**格式披露 | 数据能泄露吗? | 加密、 字段允许列表、 通用错误 |
| **D**服役情况 |  它能承受吗? | 速率限制、输入大小限制、超时 |
| **E**特权的授予 | 用户能否获得他们不该获得的权益? | 授权检查,最少的特权 |

4. ** 在使用个案旁边写虐待案。**  对于每个功能,问“我怎么会滥用这个?” ——然后做你的第一次测试。

 如果你不能为一个特性命名信任边界,你还没有准备好保证它的安全。 OWASP **A04:不可靠的设计** ——大多数违反行为都是从设计开始,而不是从代码开始.

##  三端边界系统

### 总是如此 ( 无例外)

- **校验所有外部输入**  在系统边界上 (API 路径, 窗体处理器)
- **参数化所有数据库查询** ——从不将用户输入到SQL
- **编码输出** 防止危险品扩散 (使用框架自动逃避,不要绕过它)
- **使用( U) HTTPS** 用于所有外部通信
- **散列密码** 与 bcrypt/scrypt/argon2 (永远不存储纯文本)
- **设置安全信头** (CSP, HSTS, X - Frame - 选项, X - Content - Type - 选项)
- **仅使用 httply, security, sameSite cookie** 届会
- **运行检测到的软件包管理器本地审计**  在每次释放前 都用锁文件

###  先问 (需要人类批准)

- 添加新的认证流或更改认证逻辑
- 储存新的敏感数据类别 (PII,付款信息)
- 增加新的外部服务整合
- 更改 CORS 配置
- 添加文件上传处理器
- 修改限制率或节奏
- 授予更高的权限或角色

### 永远不要做

- **永远不要做出秘密** 转到版本控件 (API 密钥、 密码、 令牌)
- **永远不要日志敏感数据** (密码、代号、信用卡号码)
- **永远不要信任客户端验证**  作为安全边界
- **永远不要禁用安全信头**  为方便起见
- **永远不要使用 `eval()` 或 `innerHTML`** 使用用户提供的数据
- **永远不要在客户端可访问存储中存储会话** (Authors 令牌的本地图案)
- **永远不要暴露堆栈痕迹** 向用户提供内部错误细节

## 硬化控件

 下面的规则是工作流程;具体落实每个生活 [references/hardening-patterns.md](references/hardening-patterns.md)打开你到达代码时需要的部分, 而不是之前。

### 注射、XSS和出入控制

- 参数化每个查询。永远不要从输入字符串中构建 SQL 、 NoSQL 或 shell 命令 。
-  通过框架的自动逃避来编码输出。 如果原始 HTML 不可避免,先用允许的消毒剂消毒
- 检查 **授权** 不仅仅是认证:经认证的用户必须拥有或允许使用特定资源 (A01, 标识).

 图案 : [注射量](references/hardening-patterns.md#injection), [一般事务](references/hardening-patterns.md#cross-site-scripting-xss), [访问控制](references/hardening-patterns.md#broken-access-control).

### 认证和会话

- 带有 bcrypt 的 Hash 密码 ( * 12发), scrypt, 或 argon2. 会话秘密来自环境, 从来不来自代码.
-  会话 cookie 是 `httpOnly`, `secure`,以及 `sameSite: 'lax'` 或 `'strict'` ( 中国证监会辩护人; `'none'`  在跨站点请求中发送 cookie),带有边框 `maxAge`.

模式 : [认证](references/hardening-patterns.md#broken-authentication).

### 信头、CORS和答复

-  每一次答复都有安全头条 (头盔或同等框架); CSP 起始于 `default-src 'self'` 并且收紧,而不松动。
- CORS 仅限于配置的显源列表。 永远不要 `*` 并附有全权证书。
- 剥离敏感字段 (`passwordHash`,重设符号) 。错误机构是通用的;内部只转到服务器日志。

 图案 : [配置错误](references/hardening-patterns.md#security-misconfiguration), [敏感数据暴露](references/hardening-patterns.md#sensitive-data-exposure).

### 输入验证和上传

-  在边界上用一个图案验证: 允许列出的形状、 长度、 enums、 格式。 用 422 和结构化的细节拒绝; 下游代码只使用解析的、 键入的值 。
- 上传: 允许列表 MIME 类型, 上限大小, 校验内容 (魔法字节)  当它重要的时候,扩展证明什么也没有。

 图案 : [Schema 验证](references/hardening-patterns.md#schema-validation-at-boundaries), [ 文件上传](references/hardening-patterns.md#file-upload-safety).

### 服务器端获取 (SSRF( 战略支助部队))

任意 URL 用户影响 – webhoks,导入 -URL, 图像代理, 链接预览 —— 可以针对内部服务。 允许列表方案和主机, 解析 **全部( E)** DNS记录并拒绝任何私人地址或保留地址 (循环回放, 链接- 本地 `169.254.169.254`, 私人, 独特的本地, 用于 IPv4 和 IPv6),并禁止重定向。该检查仍然有一个 DNS 装订的 TOCTOU 缺口:对于高风险表面,将解析的IP贴上,或者在前面放置过滤代理。

模式 : [SSRF( 战略支助部队)](references/hardening-patterns.md#server-side-request-forgery-ssrf).

### 衍生路径上的破坏性操作

 删除、移动或覆盖仅与给目标命名的值一样安全,信任跟随谁 *写*  该值, 而不是哪个频道发送 : 另一个进程的命令行是像窗体字段一样由攻击者控制 。 形状检查可以证明形状良好而非授权 。 在调用前, 需要全部三个: 已解析的目标 。 (符号链接已解决) 坐在一个 **允许列表根**; 至少是一个级别 **下级**  它的根,它携带着 **操作前读取的所有权证据**。在拒绝时,登录被拒绝的目标并停止;永远不要回到更广泛的默认路径。

 为什么支票比它写的要弱 (标记自我证明, check/use 种族): [破坏性路径](references/hardening-patterns.md#destructive-operations-on-derived-paths)。工作代码 : `../../references/security-checklist.md`.

### 限制费率

限制 API 通常和严格的认证端点 ( 每15分钟约10次尝试) 一旦不止一个过程为交通服务,内幕计数器就无声无息地变成 `max × instances`,或者从不向无服务器开火:用共享商店返回限制器。

模式 : [限制费率](references/hardening-patterns.md#rate-limiting).

### 秘诀

秘密来自环境。 `.env.example` 与占位符一起实施; 真实 `.env*` 文档和关键材料被表示; 执行前粘贴 diff 。 ** 一个到达遥控器的秘密一旦降落 就会被破坏:先旋转它,然后清洗历史。**

模式 : [保密管理](references/hardening-patterns.md#secrets-management).

### 依赖性和供应链

1. **查找安装边界和管理器 。** 使用拥有锁定文件的工作空间根, 或仅在工作空间之外使用独立的嵌入工程。 Corroborate `packageManager` ( 当存在时),锁文件,和 CI; 停止分歧或相竞的锁文件。按住管理器版本。
2. **第一次执行前的块依赖脚本 。** 脚本已禁用或有文件记录的失败关闭策略的脚本拖曳、检查待决脚本来源、只批准最小值、执行策略、然后用清空方式验证 frozen/immutable 安装,永远不要全副武装
3. ** 在每次发布前对已执行的锁文件进行本地审计。** 线性 critical/high 由 ** 可达到性** (运行时间、 构建、 测试、 部署路径) 并固定可用性。从不采用强制补救 (`npm audit fix --force` 或相当)  由于强制修补可能跨越已宣布的依赖范围; 预览、 读取更改日志、 测试每次升级。 记录每次推迟都有理由和审查日期 。
4. **审计只符合已知的建议。** 他们没有捕捉到新恶意或破伤风包 (`cross-env` 数字 `crossenv`)。审查新的依赖性、锁定文件 diff 和脚本政策变化:所有权、维护、发布时间、来源、中转图。在支持的情况下验证登记签名。 (`npm audit signatures`, `pnpm audit signatures`) 并视其缺席为调查的信号,而不是妥协的自动证明 (A06, LLM03 (法语)).

曲线决定树 : [依赖性审计分类法](references/hardening-patterns.md#dependency-audit-triage)。管理器矩阵和安装标记门 : `../../references/security-checklist.md`.

### 个人资料和隐私

Hardening问"攻击者能读吗?" 隐私问"应该" * 我们* “保护、破坏和遵守最便宜的数据是你从未收集过的数据;将个人数据视为尽量减少责任。

- **添加字段时进行分类** ( 非个人性质,PII,敏感) 并相应处理每类数据。您无法保护或遵守无法找到的数据的删除请求。
- **仅按规定目的收取。** “以后可能有用”是潜在的突破范围,而不是目的。 (编号 `observability-and-instrumentation` 技巧从行动方面提出相同的观点).
- **设置保留前置, 然后实际删除 。**  每个个人数据存储都需要一个TTL和一个工作删除路径,包括备份,缓存,搜索索引,以及分析副本.
- **支持您管辖权所要求的数据主体权利** (GDPR, CCPA, 和亲属):导出,正确,删除。设计计划时,用户的数据是可找到和可消除的,而不是在系统之间不可逆地涂抹。
- **同意门收集和第三方共享,可审计。** 将 PII 发送给分析、 广告或 LLM 供应商正在共享; 供应商需要一份数据处理协议。 让区域成为可配置的政策,而不是硬码的假设 。

分类表: [数据分类](references/hardening-patterns.md#data-classification)隐私事件引发了违反通知的时钟;与受害人一起进行尸检。 `debugging-and-error-recovery` 技术。

### AI/LLM 特性

调用LLM——聊天机、总结机、特工、RAG——增加了一个新的攻击表面;将其映射到 [OWASP LLM 应用程序前 10 位 (2025)](https://genai.owasp.org/llm-top-10/):

- **模型输出为不可信输入** (LLM05 数据)。从不进入 `eval`SQL,一个贝壳, `innerHTML`,或文件路径;从防御角度分析,对一个计划进行验证,然后编码。
- **警报可以被劫持** (LLM01 数据)。在上下文中不可信文本——一个用户消息,一个获取的页面,一个PDF——可以携带指令。系统提示不是一个安全边界;在代码中执行权限。
- **保留秘密,其他租户的数据,整个系统迅速从上下文窗口中退出** (LLM02, LLM07 (英语).); 范围工具权限, 验证每个工具参数, 并确认破坏性行动 (LLM06 (法语)); 盖符、 请求率和重现深度 (法学硕士10); 分区 RAG 嵌入每个租户并在索引编制前验证文档 (LLM08 常规).

模式 : [LLM 输出处理](references/hardening-patterns.md#llm-output-handling).

##  审查核对清单

注册前,走走 `../../references/security-checklist.md`:它涵盖认证、授权、输入、数据保护和隐私、信头和CORS、依赖性和供应链, AI/LLM,和错误处理,加 OWASP 快速参考表。

## Common Rationalizations


| 合理化 | 现实 |
|---|---|
| "这是一个内部工具,安全无关紧要" | 内部工具受损 攻击者瞄准最薄弱环节 |
| "我们晚点再加保安" | 安保改造比建造更难 现在就加进去 |
| "没有人会试图利用这个" | 自动扫描仪会找到的 隐蔽的安全不是安全 |
| "框架处理安全" | 框架提供了工具,而不是保证。您仍需要正确使用它们 。 |
| "这只是个原型" | 原型变成生产,从第一天开始就有安全习惯了 |
| "恐惧的模特儿是过度杀戮" | 5分钟的"我该如何攻击这个?" 防止设计缺陷 任何控制都无法在以后补补上. |
| "只是LLM输出,只有文字" |  此“ text” 可以是 SQL 语句、 脚本标签 或 shell 命令 。 把它当作任何不信任的输入 。 |
| "审计通过,所以依赖性是安全的" | 审计符合已知的建议。 它们不会检测到新的恶意软件包, 也不会让未经审查的安装脚本安全执行 。 |
| "现在收集它,我们可能需要它以后" |  你所持有的数据不能被突破,传票,或被错误删除. "Might need it"是突破范围,而不是目的. |
| "我们会手动处理删除请求" | 手动清除丢失备份,缓存,以及分析副本。如果计划无法找到用户的数据,则无法满足请求——为它设计。 |
| "遵义是法律问题,不是我们的问题" | 输出、删除、保留和同意是计划与代码。 法律无法在您将PII涂抹到10个系统之后把它们塞上。 |

## Red Flags


- 用户输入直接传递到数据库查询、 shell 命令或 HTML 渲染
-  删除、移动或覆盖目标来自有效载荷、配置值或另一个进程的命令行,仅通过路径上的形状检查加以保护
- 源代码中的秘密或承诺历史
- API  没有认证或授权检查的终点
- 缺少 CORS 配置或通配符 (`*`) 来源
-  没有限制认证端点的速率, 或在多个实例前的内嵌限制器
- 向用户暴露的堆叠痕迹或内部错误
- 存在已知关键弱点的依赖性、一个安装边界上相互竞争的锁文件、不可复制的安装或一揽子核准的脚本
- 服务器获取用户提供的 URL而不包含允许列表 (SSRF( 战略支助部队))
- LLM/model 输出传递到查询、 DOM、 shell 或 `eval`
- 隐藏, PII, 或放置在 LLM 上下文窗口内的全系统提示
-  在没有说明目的、保留限额或删除路径的情况下收集的个人数据
- PII 发送给 analytics/ad/LLM 未经同意或未经数据处理协议的供应商
- "删除我的账户",它只翻转一面旗帜,而个人数据则停留在商店和备份中

## Verification


执行与安全有关的守则后:

- [ ] 本地审计没有可触及的未解问题 critical/high 调查结果; CI 保存权威的锁文件, 并屏蔽未审查的依赖脚本
- [ ]  在源代码或git历史中没有秘密
- [ ]  在系统边界验证所有用户输入
- [ ] 解析文件系统操作在运行前先解析符号链接, 然后再验证允许的 root、 最小深度和所有权
- [ ] 在每个受保护的终点检查认证和授权
- [ ] 答复时有安全头 (使用浏览器检查 DevTools)
- [ ] 错误反应不会暴露内部细节
- [ ]  当一个以上实例为流量服务时,使用共享商店支持的认证端点限制活动率
- [ ] 服务器侧 URL  根据许可列表获取已验证的 ( 无SRSAF)
- [ ] LLM/model 使用前对输出进行验证和编码 ( 如果存在 AI 特性)
- [ ] 个人资料被分类、尽量减少到一个明确的目的,并有保留限额
- [ ]  删除和出口请求 (包括备份、缓存和分析副本)
