---
title: "[微信小程序 - 1] 基本构成及配置"
description: ""
lead: "注意：此文章发布于2018年2月，年代久远且不再更新，可能有大量过时或错误信息，仅作备份参考"
date: 2018-02-11T19:53:57+08:00
lastmod: 2018-02-11T19:53:57+08:00
draft: false
images: []
menu: 
  docs:
    parent: "wechat"
weight: 100
toc: true
---

小程序的前期准备工作可在[开发者文档](https://mp.weixin.qq.com/debug/wxadoc/dev/index.html)查询，此处掠过

## 一、代码构成

微信小程序基本文件由“页面文件（pages）”和“app文件（.js .json .wxss）”构成

### 1、JSON配置

```
{
  "pages":[
    "pages/index/index",
    "pages/logs/logs"
  ],
  "window":{
    "backgroundTextStyle":"light",
    "navigationBarBackgroundColor": "#fff",
    "navigationBarTitleText": "WeChat",
    "navigationBarTextStyle":"black"
  }
}
```

pages字段：用于对页面目录的注册（每个建立的页面都应在pages字段进行注册）

window字段：用于对小程序页面风格进行配置

### 2、WXML及WXSS配置

wxml 和 wxss 与 html 和 css 类似，但是 wxml 新增了以写常用组件的标签，并且新增了以写类似 wx:if 这样的属性。

## 二、wxml中使用换行

在<text>标签中可以使用 \n 进行换行，但若不使用标签则 \n 不进行换行。

```
normal text\n123<!--这里的\n不会换行-->
<text>\ntag text</text>
```

运行结果：

```
normal text 123
tag text
```

---
## 参考

1. [微信小程序开发者文档](https://mp.weixin.qq.com/debug/wxadoc/dev/index.html)
2. [微信小程序](https://mp.weixin.qq.com/)
3. 软谋教育课程-腾讯课堂
