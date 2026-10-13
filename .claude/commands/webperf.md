---
description: Run a web performance audit via the web-performance-auditor persona
---

`/webperf` 指定 Web 应用程序的目标。不要将其用于工具库、 CLI 或没有浏览器创建输出的仅用于服务器的代码。

## 确定模式

**深模式** —— 可用时激活:
- 灯塔 JSON 报表文件 (譬如说. `npx lighthouse <url> --output json --output-path ./report.json`,或 `npx -p chrome-devtools-mcp chrome-devtools lighthouse_audit --output-format=json`  从 Chrome DevTools MCP CLI)
- 页面显示的透视 JSON 回应 (包括灯塔+CRUX)
- A CrUX 化学文摘社 API 回应 (要求 `CRUX_API_KEY` 或 `GOOGLE_API_KEY`)
- A DevTools 性能跟踪
- 现场直播 URL 加上 `chrome-devtools` MCP 服务器已配置在控制带中 ( 该剂可直接通过 `lighthouse_audit` 财务报告和财务报告 `performance_*` 工具)
-  那个... Chrome DevTools MCP CLI 本地引用 (途经 `npx -p chrome-devtools-mcp chrome-devtools <tool>` 之后或之后 `npm i -g chrome-devtools-mcp`) ——用户运行命令像 `chrome-devtools lighthouse_audit --output-format=json` 并传递 JSON 输出到代理

**快速模式** —— 不存在上述信息时默认。代理对结构反标的源代码进行扫描,并将每个发现标注为 `potential impact`.

## 运行审计

喷出 `web-performance-auditor` 副特工 明确通过:

- 正在审查的文件、组件或 diff
- 任何文物路径 (灯塔 JSON,《防扩散安全倡议》 JSONCRUX 响应,跟踪) 粘贴 JSON 内容
- 目标 URL 或已知的页面名称
-  关于您期望哪种模式的注释 (快速或深处),因此,如果意图是Deep的话,毒剂会浮出水面。

子代理返回计分卡 (仅包含源值),列出了一系列调查结果、积极意见和积极主动的建议。

## 产出

将完整的审计报告还给用户,不需要综合或合并步骤——这是单人命令.
