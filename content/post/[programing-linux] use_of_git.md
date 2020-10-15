---
title: "git 使用"
date: 2020-05-14T14:53:28+08:00
categories:
- programing
- linux
tags:
- git
keywords:
- git
thumbnailImage: images/mapel.jpg
thumbnailImagePosition: right
metaAlignment: center
coverImage: images/mapel.jpg
coverMeta: in
coverSize: partial
---
<!--more-->

git 用户密码的保存和删除

方法1：
保存密码
```sh
git config --global credential.helper store
```

清除密码
```sh
git config --system --unset credential.helper
```

方法2：
保存密码
```sh
git remote add remotename https://username:userkey@github.com/gitname/repname.git
```

# commit之后，想撤销commit
- 写完代码后，我们一般这样
```sh
git add . //添加所有文件
git commit -m "本功能全部完成"
```

- 执行完commit后，想撤回commit，怎么办？
```sh
git reset --soft HEAD^
```
这样就成功的撤销了你的commit
注意，仅仅是撤回commit操作，您写的代码仍然保留。

- 说一下个人理解：
HEAD^的意思是上一个版本，也可以写成HEAD~1
如果你进行了2次commit，想都撤回，可以使用HEAD~2

## 其他参数

`--mixed`
意思是：不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
这个为默认参数,git reset --mixed HEAD^ 和 git reset HEAD^ 效果是一样的。

`--soft`
不删除工作空间改动代码，撤销commit，不撤销git add .

`--hard`
删除工作空间改动代码，撤销commit，撤销git add .
注意完成这个操作后，就恢复到了上一次的commit状态。

顺便说一下，如果commit注释写错了，只是想改一下注释，只需要：
`git commit --amend`
此时会进入默认vim编辑器，修改注释完毕后保存就好了。


# 正在写代码，突然线上出现了bug
最近在学习Git，如有说的不正确地方，请大神门指正。
一个很不错的学习git网站：
http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000

可以用github练习git

正在拼命的写代码，突然线上出现了一个bug，需要立刻解决，但是目前的工作空间代码改动挺大的，怎么解决？方法如下：

## 方法1：在当前主分支修改bug
暂存当前的改动的代码，目的是让工作空间和远程代码一致：

git stash

修改完bug后提交修改：

git add .
git commit -m "fix bug 1"
git push

从暂存区把之前的修改恢复，这样就和之前改动一样了

git stash pop

这时可能会出现冲突，因为你之前修改的文件，可能和bug是同一个文件，如果有冲突会提示：

Auto-merging xxx.java
CONFLICT (content): Merge conflict in xxx.java

前往xxx.java解决冲突

注意stash pop意思是从暂存区恢复到工作空间，同时删除此条暂存记录。



## 方式2：拉一个新分支，老司机都推荐这样做，充分利用了git特性
先暂存一下工作空间改动：

git stash

新建一个分支，并且换到这个新分支

git branch fix_bug //新建分支
git checkout fix_bug //切换分支

这时候就可以安心的在这个fix_bug分支改bug了，改完之后：

git add .
git commit -m "fix a bug"

切换到master主分支

git checkout master

从fix_bug合并到master分支

git merge fix_bug

提交代码

git push

然后从暂存区恢复代码

git stash pop

此时如有冲突，需要解决冲突

哈哈，工作空间又恢复到了原状

---
参考：
https://blog.csdn.net/w958796636/article/details/53611133
https://blog.csdn.net/w958796636/article/details/53609589
[git 删除远程分支上的某次提交](https://blog.csdn.net/QQxiaoqiang1573/article/details/68074847)
[git合并历史提交](https://www.cnblogs.com/woshimrf/p/git-rebase.html)
