---
title: "[微信小程序 - 2] 框架 视图层 WXML（数据绑定、渲染、引用）"
description: ""
lead: "注意：此文章发布于2018年2月，年代久远且不再更新，可能有大量过时或错误信息，仅作备份参考"
date: 2018-02-11T20:02:03+08:00
lastmod: 2018-02-11T20:02:03+08:00
draft: false
images: []
menu: 
  docs:
    parent: "wechat"
weight: 100
toc: true
---

## 一、组件与数据绑定

https://mp.weixin.qq.com/debug/wxadoc/dev/component/text.html

### 1、text组件与button组件

```
<text>text组件文本</text>
<button type="normal">normal button</button>
<button type="primary"> primary button </button>
```

这里 button 标签内 type 属性用于设置按钮风格

![运行结果](https://raw.githubusercontent.com/gitsang/gallery/master/page/202005282011.jpg)

### 2、数据绑定

数据名用 {{}} 可对其内容在 .js 文件中进行绑定

```
<text>{{bindText_1}}</text>
```

```
Page({
  data: {
    bindText_1:"这是.js中绑定的数据文本"
  },
})
```

## 二、button的点击响应

下例通过 bindtap 属性对 button 点击事件进行响应

单击按钮后其显示文字改变

```
<button type="primary" bindtap="btnclick"> {{btnText}} </button>
```

```
Page({
  data:{
    btnText:"default btn text"
  },
  btnclick: function () {
    console.log("btnclick: function");
    this.setData({btnText:"set new btn text"});
  }
})
```

## 三、条件渲染

下例点击按钮后改变条件值从而改变文本

```
<button bindtap="btnclick">button</button>
<text wx:if="{{condition}}">满足条件</text>
  <text wx:else>不满足条件</text>
```

```
Page({
  data:{
    condition:true
  },
  btnclick: function () {
    var condition = this.data.condition;
    //这里condition需要在函数内先进行定义
    //data中的condition不能直接拿来使用
    console.log(condition);
    this.setData({condition:!condition});
  }
})
```

## 四、列表渲染

在组件上使用 wx:for 控制属性绑定一个数组，即可使用数组中各项的数据重复渲染该组件。

```
<view wx:for="{{array}}">
  {{index}} ------ {{item.message}}
</view>
```

默认数组的当前项的下标变量名默认为 index，数组当前项的变量名默认为 item

```
Page({
  data: {
    array: [{
      message: '第0个文本',
    }, {
      message: '第一个文本'
    }]
  }
})
```

运行结果：

```
0 ------ 第0个文本
1 ------ 第一个文本
```

item.message即为array数组中的message变量（array.message）

上述结果与一下运行结果相同

```
Page({
  data: {
    array: [
      '第0个文本', '第一个文本'
    ]
  }
})
```

使用 wx:for-item 可以指定数组当前元素的变量名，

使用 wx:for-index 可以指定数组当前下标的变量名：

```
<view wx:for="{{array}}" wx:for-index="idx" wx:for-item="itemName">
  {{idx}}: {{itemName.message}}
</view>
```

## 五、页面引用

### 1、include

include 可以将目标文件除了 `<template/>` `<wxs/>` 外的整个代码引入，相当于是拷贝到 include 位置，如：

```
<!-- pages/demo/demo.wxml -->
<include src="../template/header.wxml"/>
<view> body </view>
```

```
<!-- pages/template/header.wxml -->
<view> header </view>
```

### 2、template

下例 import 了 footer.wxml 使用其中名为 footer2 的模板

```
<!-- pages/demo/demo.wxml -->
<!--此处对footer.wxml引入，还未分配模板-->
<import src="../template/footer.wxml"/>
<!--此处决定模板的使用，同时能对模板为定义的数据进行定义-->
<template is="footer2" data="{{text: 'footer template'}}"/>
```

```
<!-- pages/template/footer.wxml -->
<template name="footer1">
  <text>{{text}} 1</text>
</template>

<template name="footer2">
  <text>{{text}} 2</text>
</template>
```

### 3、import 的作用域

import 有作用域的概念，即只会 import 目标文件中定义的 template，而不会 import 目标文件 import 的 template。

***如：C import B，B import A，在C中可以使用B定义的template，在B中可以使用A定义的template，但是C不能使用A定义的template。***
