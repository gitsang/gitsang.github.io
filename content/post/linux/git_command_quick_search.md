---
title: Git Command Quick Search
slug: git-command-quick-search
description: This document provides detailed instructions on various Git operations, including password management, resetting commits, handling commits, and modifying remote repositories. It also covers advanced topics like auto-conversion of line endings and merging commits.
date: "2025-05-12T10:46:23+08:00"
lastmod: "2025-05-12T10:46:23+08:00"
weight: 1
categories:
  - "linux"
tags:
  - "Git commands"
  - "commit management"
  - "branching"
  - "reset operations"
  - "password storage"
  - "stash"
  - "rebase"
---

<!-- markdown-front-matter -->

## 1. git 用户密码的保存和删除

```
# 保存密码
git config --global credential.helper store

# 清除密码
git config --system --unset credential.helper
```

```
git remote set-url remotename https://username:password@github.com/gitname/repo_name.git
```

## 2. git reset

### 2.1 撤销 commit (soft)

```
# 撤销上一条 commit
git reset --soft HEAD^

# 撤销上n条 commit
git reset --soft HEAD^n

# 撤销 commit 到某个 commit-version
git reset --soft <commit-version>
```

soft reset 仅仅撤销提交不回滚代码

### 2.2 撤销 commit 和 add (mixed)

```
git reset --mixed HEAD^
```

### 2.3 修改 commit 内容 (amend)[^3]

```
git commit --amend
```

### 2.4 回滚代码 (hard)

```
git reset --hard <commit-version>
```

hard reset 会将代码强制回滚

如果需要强制提交到远端

```
git push -f origin master
```

强制 push 会导致当前 commit 之后的所有提交永久性丢失

## 3. 查看 commit-version

```
# 查看远端的 commit log
git log

# 查看本地的 commit log
git reflog
```

如果你的队友 `push -f` 了代码，而你又不幸 `pull` 了代码，可以通过 reflog 找回你之前的本地提交

## 4. 代码入栈[^1]

pop 之后可能需要解决冲突

```
# 当前代码入栈，并恢复到线上的 commit
git stash

# 修改代码并提交
git add --all
git commit -m "fix"
git push

# 代码出栈
git stash pop
```

## 5. 代码入栈并拉取新分支[^2]

```
# 当前代码入栈，并恢复到线上的 commit
git stash

# 新建分支
git branch master-dev-fix
# 切换分支
git checkout master-dev-fix
# 也可以新建并切换分支
#git checkout -b master-dev-fix

# 修改代码并提交
git add --all
git commit -m "fix"

# 合并到 master
git checkout master
git merge master-dev-fix

# push 到线上
git push

# 恢复代码
git stash pop
```

## 6. 自动转换换行符

```
# 提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf true

# 提交时转换为LF，检出时不转换
git config --global core.autocrlf input

# 提交检出均不转换
git config --global core.autocrlf false
```

## 7. 使用字符串而不是 ascii 码输出

```
git config --global core.quotepath false
```

## 8. 修改远程仓库地址

```
git remote set-url origin https://giturl.git
```

## 9. 合并提交[^4]

```
git rebase -i HEAD~3
# set merge commit to squash
```

## 10. 参考

[^1]: [git使用情景1：正在写代码，突然线上出现了bug](https://blog.csdn.net/w958796636/article/details/53609589)

[^2]: [git使用情景2：commit之后，想撤销commit](https://blog.csdn.net/w958796636/article/details/53611133)

[^3]: [git 删除远程分支上的某次提交](https://blog.csdn.net/QQxiaoqiang1573/article/details/68074847)

[^4]: [git合并历史提交](https://www.cnblogs.com/woshimrf/p/git-rebase.html)
