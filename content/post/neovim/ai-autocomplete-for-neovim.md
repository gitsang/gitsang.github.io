---
title: '在 Neovim 中实现 AI 自动补全'
description: ''
slug: ai-autocomplete-for-neovim
date: 2025-02-25T09:28:45+08:00
lastmod: 2025-02-25T09:28:45+08:00
weight: 1
categories:
  - neovim
tags:
  - neovim
  - llm
  - ai
---

## 讨论

这里有关于 Neovim Autocomplete 和其他 AI 插件的讨论：https://github.com/neovim/neovim/discussions/28162

这里有一个 continue-dev 支持 neovim 的 PR，但由于缺乏审查，已被关闭：https://github.com/continuedev/continue/pull/2098

这是 continue-dev 中的讨论 Issue，https://github.com/continuedev/continue/issues/917

## 插件列表

### 对话和修改

- https://github.com/yetone/avante.nvim 目前 Star 数量最多的插件，但在非 Lazyvim 中界面展示并不美好，还有一些奇怪的渲染，比如强制对 Markdown 渲染让人有些恼火
- https://github.com/olimorris/codecompanion.nvim 似乎必 Avante 更容易配置，且界面更加友好
- https://github.com/Robitx/gp.nvim 讨论度比较高的插件
- https://github.com/frankroeder/parrot.nvim 基于 gp.nvim 的 fork

### 自动补全

- https://github.com/huggingface/llm.nvim
