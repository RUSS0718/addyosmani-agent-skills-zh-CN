# 简化黄麻钩

区级保护 `/code-simplify`. 标记永远不应简化的代码——模型不会看到它.

## 设置

1.  您想要保护的注释块 :

```js
/* simplify-ignore-start: perf-critical */
// manually unrolled XOR — 3x faster than a loop
result[0] = buf[0] ^ key[0];
result[1] = buf[1] ^ key[1];
result[2] = buf[2] ^ key[2];
result[3] = buf[3] ^ key[3];
/* simplify-ignore-end */
```

2. 添加钩子到 `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Read",
        "hooks": [{ "type": "command", "command": "bash \"${CLAUDE_PROJECT_DIR}/hooks/simplify-ignore.sh\"" }]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{ "type": "command", "command": "bash \"${CLAUDE_PROJECT_DIR}/hooks/simplify-ignore.sh\"" }]
      }
    ],
    "Stop": [
      {
        "hooks": [{ "type": "command", "command": "bash \"${CLAUDE_PROJECT_DIR}/hooks/simplify-ignore.sh\"" }]
      }
    ]
  }
}
```

3. 运行 `/code-simplify` - 受保护的区块成为 `/* BLOCK_de115a1d: perf-critical */` 占位符 。 代码周围的模型原因没有看到受保护的执行 。

> **说明:** 钩子将临时备份存储在 `.claude/.simplify-ignore-cache/`。确定这条路径在您的 `.gitignore`.

## 如何运作

 一个脚本,三个钩子事件:

| 活动 | 行动 |
|---|---|
| `PreToolUse Read` | 备份文件, 将块替换为 `BLOCK_<hash>` 占位符在位数 |
| `PostToolUse Edit\|Write` | 将占位符扩展回真实代码, 保存模型的更改, 重新过滤 |
| `Stop` | 将磁盘上每个文件中留下的占位符展开, 因此在外部更改 `Edit`/`Write` 幸存; 只有在没有占位符时才返回备份 |

 每个区块都装满了内容 (8 x 字符通过 `shasum`/`sha1sum`)  因此,即使模型重复或重排占位符,圆路也是毫不含糊的。缓存是工程范围,以防止交叉会话干扰。

## 注释语法

```js
/* simplify-ignore-start */           // basic — hides the block
/* simplify-ignore-start: reason */   // with reason — appears in placeholder
/* simplify-ignore-end */
```

任何评论风格的作品 (`//`, `/*`, `#`, `<!--`)。每个文件和单行块都支持多个块。位置符保存了原注释语法 (譬如说. `# BLOCK_xxx` (单位:千美元) Python, `<!-- BLOCK_xxx -->` (单位:千美元) HTML).

## 崩溃恢复

 若为: Claude Code  在不触发停止钩的情况下崩溃,磁盘上的文件可能仍然有 `BLOCK_<hash>` 占位符。要手动恢复 :

```bash
echo '{}' | bash hooks/simplify-ignore.sh
```

备份存储于 `.claude/.simplify-ignore-cache/`  在您的项目目录中。

## 已知的限制

- **单线块隐藏全线.**  若为: `simplify-ignore-start` 财务报告和财务报告 `simplify-ignore-end` 显示与其他代码相同的线条,整个线条隐藏在模型中,而不只是附加说明的部分。使用专用线条作为说明。
- **注释后缀检测封面 `*/` 财务报告和财务报告 `-->` 仅此而已。** 带有非标准注释的模板引擎更接近 (ERB 经常预算 `%>`刀锋 `--}}`) 可能会产生不平衡的占位符。 `#` 或 `//` 样式注释代替。
- **退步扩张是渐进的,而不是精确的.**  如果模型改变占位符的格式 ( 例如,更改理由文本),钩子会尝试逐渐更简单的匹配: 完全占位符 → 前缀+hash+safdix → hash- only. ( 例如,误差 `:` 或理由文本)。一旦发生这种情况,就会向 Stderr 打印警告。
- ** 一个被移除的占位符掉回备份.**  如果编辑删除一个 `BLOCK_<hash>` 线条,或某物取代整个文件,钩子无法再分辨该受保护块属于何处。 `Edit` 或 `Write` 报告给模型。 (2号出口) 以占位符恢复,且备份不更新。如果占位符仍然丢失在 `Stop`,勾钩恢复备份并打印一个警告;已更改的文件不丢失,而是保存在缓存中。 `.claude/.simplify-ignore-cache/<id>.recovered`,其路径是警告的指纹,你用手将它重新组合起来。使占位符处于位置的改变——a `Bash` 编辑,一个前题,一个外部编辑器——保存在文件本身中.
- ** 文件重命名叶占位符 。**  如果模型通过 shell 命令重命名或移动文件,则新文件将保留 `BLOCK_<hash>` 占位符。原始代码保存为 `<old-filename>.recovered`  当会话停止时,您必须手动将恢复的代码恢复到新文件中。

## 所需经费

- `jq`, `shasum` 或 `sha1sum` (自动检测),巴什 3.2+
