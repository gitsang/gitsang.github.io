---
title: "[微信小程序 - 4] 组件-基础内容（icon、text、progress）"
description: ""
lead: "注意：此文章发布于2018年2月，年代久远且不再更新，可能有大量过时或错误信息，仅作备份参考"
date: 2018-02-11T20:17:57+08:00
lastmod: 2018-02-11T20:17:57+08:00
draft: false
images: []
menu: 
  docs:
    parent: "wechat"
weight: 100
toc: true
---

## 一、icon

属性名 | 类型 |	默认值 | 说明
---|---|---|---
type|String| |icon类型，有效值：success, success_no_circle, info, warn, waiting, cancel, download, search, clear
size|Number|23|icon的大小，单位px
color|Color| |icon的颜色，同css的color

## 二、text

space 有效值：

值 | 说明
-|-
ensp|中文字符空格一半大小
emsp|中文字符空格大小
nbsp|根据字体设置的空格大小

Tips
: decode可以解析的有 `&nbsp; &lt; &gt; &amp; &apos; &ensp; &emsp;`
: 各个操作系统的空格标准并不一致。
: `<text/>` 组件内只支持 <text/> 嵌套。
: 除了文本节点以外的其他节点都无法长按选中。

## 三、progress ##

可以通过以下实例了解进度条属性

```
<progress percent="20" show-info />
<progress percent="40" stroke-width="12" />
<progress percent="60" color="pink" />
<progress percent="80" active />
```
