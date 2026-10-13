# sdd-cache 钩

交叉会话引用缓存 [`source-driven-development`](../skills/source-driven-development/SKILL.md)。跳过冗余 `WebFetch`  在不削弱技能的情况下,通过电话“验证当前文件”的保证。

##  为什么

`source-driven-development`  为每个特定框架的决定获取官方的文档。 在整个会话中进行相同的项目意味着反复获取相同的页面。 将内容切换为本地内存会与技能相矛盾 — 文档更改, 而一个 stale 缓存会隐藏它 。

 这个钩子缓存从磁盘上获取内容, 但是 ** 在每次重复使用时使用源服务器重新验证** 途经 HTTP `If-None-Match` / `If-Modified-Since`。仅在服务器响应时从缓存中提供内容 `304 Not Modified`,这是一种新的验证,而不是读取的记忆。

## 设置

1. 添加钩子到 `.claude/settings.json` (或 `.claude/settings.local.json` 供个人使用):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "WebFetch",
        "hooks": [
          {
            "type": "command",
            "command": "bash \"${CLAUDE_PROJECT_DIR}/hooks/sdd-cache-pre.sh\"",
            "timeout": 10
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "WebFetch",
        "hooks": [
          {
            "type": "command",
            "command": "bash \"${CLAUDE_PROJECT_DIR}/hooks/sdd-cache-post.sh\"",
            "async": true,
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

   `${CLAUDE_PROJECT_DIR}` 解析您启动的目录 Claude Code 。当钩子生活在同一个工程内时,上面的片段会起作用。如果安装了的话。 `agent-skills` 其他地方 ( 例如,作为共享插件 `~/agent-skills`),替换 `${CLAUDE_PROJECT_DIR}/hooks/...`  每个脚本的绝对路径。

2. 记得 `.claude/sdd-cache/`  是属于你的 `.gitignore` (已包含在本 repo 中).

3. 使用( U) `/source-driven-development` (或技能)  如往常一样。不改变技能和代理工作流程——缓存是透明的。

## 心理模式

HTTP 资源缓存按键 URL新鲜度通过下列途径授予原产地: `ETag` / `Last-Modified`; 没有 TTL, 键上没有提示 。

储存的尸体不是生的 HTML — `WebFetch` 后处理每个响应时使用调用器的提示模式, 所以我们缓存的是一个代理对页面的读取。 key stays URL-只读到跨会话的重复使用;原始提示作为元数据保存,并在命中消息中浮出水面,这样下一位代理人就可以分辨早期的读取是否合适.

## 如何运作

 每个缓存条目 URL,存储为 JSON 输入 `.claude/sdd-cache/<sha>.json`:

| 活动 | 行动 |
|---|---|
| `PreToolUse WebFetch` |  如果存在条目,则发送 `HEAD` 请求与 `If-None-Match` / `If-Modified-Since`打开 `304`中,阻断抓取,并通过 stderr 将缓存的内容返回代理商,原始的提示作为元数据浮出水面。否则,可以抓取。 |
| `PostToolUse WebFetch` | 获得答复,问题a `HEAD` 请求记录当前 `ETag` / `Last-Modified`,并储存 `{url, prompt, etag, last_modified, content, fetched_at}`. |

**新鲜度规则 :**

- 只有在来源确认时才送达条目 `304 Not Modified`.
-  没有条目 `ETag` 或 `Last-Modified` 头头永远不会被缓存——没有验证器,钩子以后无法验证新鲜度,缓存意味着信任内存.
- 缓存密钥是 `sha256(url)`。同样的 URL 以不同的快捷键点击同一条目; 缓存的机体反映了第一次抓取时使用的快捷键, 且该快捷键与命中键并列显示, 这样代理可以手动决定是重新使用还是重新牵引 。

**特工所见:**

-  快取键 : `WebFetch`  通过2号出口被封锁。 Claude Code 将钩子的Stderr有效载荷作为工具错误运回代理器——这是缓存命中的预定信号,而不是失败。有效载荷的前缀是: `[sdd-cache] Cache hit for <url>` 并覆盖缓存的正文 `----- BEGIN CACHED CONTENT -----` / `----- END CACHED CONTENT -----` 标记,使代理可以像使用一样使用 `WebFetch` 刚还回来的
- 缓存丢失或 stale : `WebFetch` 运行正常;结果保存到下次。

技能本身没有变化,继续沿用 `DETECT → FETCH → IMPLEMENT → CITE`。钩子只会改变在引擎盖下发生的事情,当 `FETCH` 运行。

## 本地测试

### 1. 直接抽烟测试脚本

```bash
# Simulate a PostToolUse payload: cache a page
echo '{
  "tool_input": {
    "url": "https://react.dev/reference/react/useActionState",
    "prompt": "extract the signature"
  },
  "tool_response": "useActionState(action, initialState) returns [state, formAction, isPending]"
}' | bash hooks/sdd-cache-post.sh

# Inspect the stored entry
ls .claude/sdd-cache/
cat .claude/sdd-cache/*.json | jq .

# Simulate the next PreToolUse on the same URL + prompt
echo '{
  "tool_input": {
    "url": "https://react.dev/reference/react/useActionState",
    "prompt": "extract the signature"
  }
}' | bash hooks/sdd-cache-pre.sh
echo "exit=$?"
```

 预期:

-  第一个命令在下面创建一个文件 `.claude/sdd-cache/` ( 只有服务器返回一个 `ETag` 或 `Last-Modified`).
-  第二个命令出口 `2`  当源代码回复时, 含有 stderr 上的缓存内容 `304`键,或退出 `0` 默然不语.

### 2. 真正的届会结束至结束

1. 注册钩入 `.claude/settings.local.json`  如上所示。
2. 开始 a Claude Code  此重播时的会话 。
3.  请代理服务器获取文档页面 ( 例如:"牵强" `https://react.dev/reference/react/useActionState` 并总结".).
4. 校验一个文件出现在下面 `.claude/sdd-cache/`.
5.  请代理再次以相同的提示获取同一页 。
6. 验证第二个 `WebFetch` 被屏蔽并返回缓存内容 (显示在会话记录中为工具错误 `[sdd-cache]`  前缀).

### 3. 新鲜度核查

 要确认文件更改时缓存无效,请强制 。 `ETag` 错配 选择一个特定条目... `*.json`  当缓存持有一个以上文件时不安全 :

```bash
# Pick the entry you want to corrupt (swap in the actual filename)
ENTRY=.claude/sdd-cache/e49c9f378670cfbb1d7d871b6dee16d9.json

# Patch its ETag to something the origin will not recognize
jq '.etag = "W/\"stale-etag-forced\""' "$ENTRY" > "$ENTRY.tmp" && mv "$ENTRY.tmp" "$ENTRY"

# Next PreToolUse should miss (server returns 200, not 304)
echo '{"tool_input":{"url":"...", "prompt":"..."}}' | bash hooks/sdd-cache-pre.sh
echo "exit=$?"   # expect 0 (fetch allowed through)
```

### 4. 调试

 两个钩子都写有时间标记的事件到 `.claude/sdd-cache/.debug.log`  当调试模式打开时。启用其中任一模式 :

```bash
# Option A: env var (per-session)
SDD_CACHE_DEBUG=1 claude

# Option B: sentinel file (persistent)
mkdir -p .claude/sdd-cache && touch .claude/sdd-cache/.debug
# …disable with: rm .claude/sdd-cache/.debug
```

日志抓取 URL检测到 `tool_response` 形状, 头状态, 以及为什么每次引用都点击或错过。 当缓存丢失看起来出乎意料时有用 (典型:原产地停止排放验证器).

## 已知的限制

- **身体为即时形状.** 命中返回先前代理对页面的读取, 原始的提示浮出水面, 以便当前代理可以决定是否适用。 如果不适用, 删除下面的文件 。 `.claude/sdd-cache/` 以强制重排。
- ** 每个缓存写要多一个头部。** Claude Code  不暴露 反应信头 `WebFetch` 已经收到,所以后钩子重新对源头进行批量以抓取 `ETag` / `Last-Modified`. 每错一次额外的圆圈旅行——保持这个纯的钩子,没有核心变化的代价.
- ** 没有服务器 `ETag` 或 `Last-Modified` 永远不会被缓存。** 多数官方网站 (反应.dev, docs.djangoproject.com,开发商.mozila.org) 发出校验器。 并不总是重排的站点 。
- **行为不检的服务器可能服务于错误 `304`.**  这是一个用于诊断的服务器错误,而不是用于防御的缓存不变量;我们不用TTL来打印它。如果发现一个呆滞的条目,就删除该条目。
- **缓存是本地和每个项目。**  没有全团队共享缓存。 添加一个将需要一个签名内容可处理的存储层, 这超出了范围 。

## 所需经费

- `jq`
- `curl`
- `shasum` 或 `sha256sum` (自动检测)
- 3.2+ 巴什语
