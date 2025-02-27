---
title: '基于 llm-ls 的 AI 自动补全'
slug: llm-ls-autocomplete
description: |-
  本文档介绍了 llm-ls 对接本地或远程 AI Coder 模型，以支持 AI 自动补全的方法。
date: 2025-02-27T10:22:52+08:00
lastmod: 2025-02-27T10:22:58+08:00
weight: 1
categories:
  - llm
tags:
  - llm
  - coder
  - autocomplete
---

## 1. 概述

llm-ls 是 huggingface 开源的一个 lsp，可以支持对接大模型提供代码补全能力。

项目地址：https://github.com/huggingface/llm-ls

目前支持的 IDE 有：

- llm.nvim
- llm-vscode
- llm-intellij

> 在不进行额外调教的前提下（目前并未找到较好的调教参数），llm-ls 的补全效果与 codium 有明显差距。

## 2. 安装

### 2.1 Neovim

#### 2.1.1 使用 vim-plug

```vim
call plug#begin()
    Plug 'huggingface/llm.nvim'
call plug#end()

require('llm').setup({
-- cf Setup
})
```

示例配置请参考：https://github.com/huggingface/llm.nvim?tab=readme-ov-file#setup

## 3. 配置

### 3.1 配置说明

对于 llm-ls，其配置可能有些复杂，并且针对不同的模型，可能需要不同的调教，
这是一个比较复杂的过程，可能需要经过长时间的实验，才能达到相对理想的效果。

但这里有一些总结的经验，可能对你调教有些帮助：

- 您应该将 temperature 降低到较小的值，以获得更规范的输出。
- 添加 suffix options 可能是一个魔法，但他在某些情况下可能真的有用，特别是在你经常看到补全代码输出分析而不是实际代码时。
- 启用 FIM 可能并不能帮助你更好的补全，甚至有可能起到反作用，及时你的模型声称支持 FIM，但如果没有特别说明其需要使用的 FIM 参数，建议不要启用 FIM。
- stop options 能在一定程度上阻止补全出现代码块边界而不是代码，但它并不总是有用。如果你的 temperature 较高，这可能会有些帮助。
- keepalive 参数可能会对性能较差的机器产生一些积极影响。

关于 temperature 和 top_p 参数，你可能需要知道：

这两个参数都在控制输出的灵活性和创新型，但实际有那么些不同，我们可以用经典 DND 九宫格来抽象的表示这一关系：

| top_p\temperature | 低温     | 中温     | 高温     |
| ----------------- | -------- | -------- | -------- |
| 低核采样          | 守序善良 | 中立善良 | 混沌善良 |
| 中核采样          | 守序中立 | 绝对中立 | 混沌中立 |
| 高核采样          | 守序邪恶 | 中立邪恶 | 混沌邪恶 |

> 通常在输出技术类文章时，倾向于使用低 Temperature 使其输出更符合规范的内容，可以适当提高 top_p 使其输出更具灵活性。
> 而在输出一些诗歌、散文时，则倾向于使用高 Temperature 使其输出更具创造力，可以适当降低 top_p 使其输出不至于失控（降低其输出不当言论的概率）。

## 配置参考

````lua
backend = "ollama",
url = "http://localhost:11434",
model = "qwen2.5-coder:7b",
tokens_to_clear = { "<|endoftext|>" },
fim = {
  enabled = false,
  prefix = "<|fim_prefix|>",
  middle = "<|fim_middle|>",
  suffix = "<|fim_suffix|>",
},
request_body = {
  suffix = "\n",
  options = {
    temperature = 0.01,
    top_p = 0.2,
    num_predict = 4096,
    num_ctx = 8192,
    stop = {
      "<|endoftext|>",
      "<|fim_prefix|>",
      "<|fim_middle|>",
      "<|fim_suffix|>",
      "<|fim_pad|>",
      "<|repo_name|>",
      "<|file_sep|>",
      "<|im_start|>",
      "<|im_end|>",
      "/src/",
      "#- coding: utf-8",
      "```",
    },
  },
  keep_alive = 1800,
},
debounce_ms = 150,
accept_keymap = "<Tab>",
dismiss_keymap = "<S-Tab>",
tls_skip_verify_insecure = false,
lsp = {
  bin_path = "/home/debian/.local/src/llm-ls/target/release/llm-ls",
  host = nil,
  port = nil,
  cmd_env = { LLM_LOG_LEVEL = "DEBUG" },
  version = "0.5.3",
},
tokenizer = nil,
context_window = 8192,
enable_suggestions_on_startup = true,
enable_suggestions_on_files = "\*",
disable_url_path_completion = false,
````

### 3.2 后端配置

#### 3.2.1 Ollama

ollama 在参数配置与 huggingface 有一些不同，主要是 request_body 参数使用 options 而不是 parameters

```lua
backend = "ollama",
url = "http://localhost:11434",
model = "qwen2.5-coder:7b",
```

#### 3.2.2 OpenAI

与 Ollama 配置基本一致

```lua
backend = "openai",
url = "https://api.siliconflow.cn/v1",
api_token = "sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
model = "Qwen/Qwen2.5-Coder-7B-Instruct",
```

## 4. 其他问题

llm-ls 二进制不可用：请尝试从 release 中下载静态编译版本，或 clone 项目代码从源码编译
