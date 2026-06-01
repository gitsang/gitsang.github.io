# Claude Code vs Opencode

## Claude Code 缺点

### Slash Command 被当作 Prompt

使用 `/` Slash Command 会被当做 prompt 发送给 LLM

这会导致：

- 运行时无法查看 context（因为 `/context` Slash Command 需要发送，所以运行时会查看会进入 Prompts 队列）
- 有时候运行 `/context` 会先发送 prompt (BUG)，LLM 先思考一轮，才给你输出

### 所有命令都挤在 Slash Command

所有的命令都挤在 `/` Slash Command 里面，Skills 装的一多，要找原生命令根本找不到

Opencode 至少还有个 `ctrl + p` 可以开原生的命令

### 会话管理的标题不会自动总结

Resume 的标题居然是使用最后一个 prompt 作为标题，如果最后一个 prompt 是 Slash Command，这个 Session 几乎就搜索不到了

```
❯ /resume
────────────────────────────────────────────────────────────────────────────────

Resume Session
╭──────────────────────────────────────────────────────────────────────────────╮
│ ⌕ Search…                                                                    │
╰──────────────────────────────────────────────────────────────────────────────╯

  /statusline
│ 6 minutes ago · draft · 17.9KB
│
│ Type to Search · Enter to select · Esc to clear
```

### 切换模型困难

因为供应商绑定，每次切换供应商，模型，都要去改 json 文件，很麻烦

### Verbose output 太难看了

verbose output 只能用 ctrl+o 查看全部的 output，根本对不上是哪个的输出。

opencode 至少能一个一个展开

## Claude Code 优点

### 全局统计

有个非常棒的全局统计 `/stats`

```
❯ /stats

────────────────────────────────────────────────────────────────────────────────
    Overview   Models  (←/→ or tab to cycle)


      Mar Apr May Jun Jul Aug Sep Oct Nov Dec Jan Feb Mar
      ····················································
  Mon ····················································
      ···················································
  Wed ···················································
      ···················································
  Fri ··················································▒
      ··················································█

      Less ░ ▒ ▓ █ More

  All time · Last 7 days · Last 30 days

  Favorite model: glm-5           Total tokens: 2.6m

  Sessions: 19                    Longest session: 2d 17h 59m
  Active days: 2/2                Longest streak: 2 days
  Most active day: Mar 6          Current streak: 0 days

  You've used ~6x more tokens than A Game of Thrones

    Esc to cancel · r to cycle dates
```

### Context 统计

Claude 的统计信息确实做的蛮全的，比如 context 还会统计 MCP Tools, Skills 消耗的 token

这个 Context Usage 做的我也很喜欢

```
❯ /context
  ⎿  Context Usage
     ⛀ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   glm-5 · 1k/200k tokens (1%)
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   Estimated usage by category
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   ⛁ Skills: 1.1k tokens (0.6%)
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   ⛶ Free space: 166k (82.9%)
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶   ⛝ Autocompact buffer: 33k tokens (16.5%)
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶
     ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶ ⛶
     ⛶ ⛶ ⛶ ⛝ ⛝ ⛝ ⛝ ⛝ ⛝ ⛝
     ⛝ ⛝ ⛝ ⛝ ⛝ ⛝ ⛝ ⛝ ⛝ ⛝

     MCP tools · /mcp
     └ mcp__chrome-devtools__click: 0 tokens
     └ mcp__chrome-devtools__close_page: 0 tokens
     └ mcp__chrome-devtools__drag: 0 tokens
     ...
     └ mcp__chrome-devtools__type_text: 0 tokens
     └ mcp__chrome-devtools__upload_file: 0 tokens
     └ mcp__chrome-devtools__wait_for: 0 tokens

     Skills · /skills

     User
     └ docx: 198 tokens
     └ pptx: 173 tokens
     ...
     └ find-skills: 79 tokens
     └ brainstorming: 53 tokens
     └ humanizer-zh: 40 tokens
```

### 会话管理能切换数据来源

可以切换到全局，而 Opencode 只能是当前目录下的数据

```
Ctrl+A to show current dir · Ctrl+B to toggle branch · Ctrl+V to preview ·
Ctrl+R to rename · Type to search · Esc to cancel ·
```
