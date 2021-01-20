---
title: "[微信小程序 - 3] 组件-视图容器（ScrollView和Swiper）"
description: ""
lead: "注意：此文章发布于2018年2月，年代久远且不再更新，可能有大量过时或错误信息，仅作备份参考"
date: 2018-02-11T20:14:57+08:00
lastmod: 2018-02-11T20:14:57+08:00
draft: false
images: []
menu: 
  docs:
    parent: "wechat"
weight: 100
toc: true
---

## 一、scroll-view

### 1、基本设置

首先是参考的开发文档网址
https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxml/

新建一个wxml文件，添加scroll-view组件，组件中添加“绿、红、黄、蓝”四种颜色的view组件（view组件较为简单，与html中div类似，此处省略，可参考官方开发文档中对view组件的说明）

需要注意的是在这里只是添加了每个view组件的id和class，还需要再wxss文件中添加其样式

这里设置scroll-view组件为竖向滚动，使用竖向滚动时，需要给`<scroll-view/>`一个固定高度，通过 WXSS 设置 height为250px

```
<scroll-view style="height:250px" scroll-y="true">
  <view id="green" class="scroll-view-item bc_green"></view>
  <view id="red"  class="scroll-view-item bc_red"></view>
  <view id="yellow" class="scroll-view-item bc_yellow"></view>
  <view id="blue" class="scroll-view-item bc_blue"></view>
</scroll-view>
```

以下是wxss中对样式的设置，`.scroll-view-item` 对4个view组件同时设置高度为200px，`.bc_green` 等分别设置每个组件不同的背景颜色。

```
.scroll-view-item {
  height: 200px;
}

.bc_green {
  background-color: green;
}

.bc_red {
  background-color: red;
}

.bc_yellow {
  background-color: yellow;
}

.bc_blue {
  background-color: blue;
}
```

### 2、滚动响应事件

以下分别说明`bindscrolltoupper`（滚动到顶部/左边触发）、`bindscrolltolower`（滚动到底部/右边触发）、
`bindscroll`（滚动时触发）三个事件响应

将wxml文件改写为如下

```
<scroll-view style="height:250px" scroll-y="true" 
  bindscrolltoupper="upper" bindscrolltolower="lower" bindscroll="scroll" >
  <view id="green" class="scroll-view-item bc_green"></view>
  <view id="red"  class="scroll-view-item bc_red"></view>
  <view id="yellow" class="scroll-view-item bc_yellow"></view>
  <view id="blue" class="scroll-view-item bc_blue"></view>
</scroll-view>
```

为bindscrolltoupper、bindscrolltolower、bindscroll属性添加名为“upper、lower、scroll”的function
在.js文件中添加响应

```
Page({
  upper: function (event) {
    console.log("to top now");
  },
  lower: function (event) {
    console.log("to low now");
  },
  scroll: function (event) {
    console.log("scrolling now");
  }
})
```

滚动时就能看到Console窗口中显示出log内容

### 3、scroll-into-view 与 scroll-top

scroll-into-view属性，值应为某子元素id（id不能以数字开头）。设置哪个方向可滚动，则在哪个方向滚动到该元素
scroll-top属性，值为竖向滚动条滚动到的位置

为`scroll-view`组件添加`scroll-into-view`与`scroll-top`属性，并添加两个button。

```
<scroll-view style="height:250px" scroll-y="true" 
scroll-into-view="{{toView}}" scroll-top="{{scrollTop}}">
	...
</scroll-view>
<view>
  <button bindtap="tap">click me to scroll into view</button>
  <button bindtap="tapMove">click me to scroll</button>
</view>
```

在.js中添加点击function——tap和tapMove
`data`中定义`toView`初始值为`'red'` ，滚动条开始与200px的位置

其中`tap: function (e) {}` 判断当前`toView` 值与`order[i]` 值是否匹配，相同则将`order[i + 1]` 的值赋给`toView` ，即每次点击都将下一个颜色`id`传给`scroll-into-view` 属性。

`tapMove: function (e) {}` 将`scrollTop` 值自加10，即滚动条位置增加10。 

```
var order = ['red', 'yellow', 'blue', 'green', 'red']
Page({
  data: {
    toView: 'red',
    scrollTop: 200
  },
  
  tap: function (e) {
    for (var i = 0; i < order.length; ++i) {
      if (order[i] === this.data.toView) {
        this.setData({
          toView: order[i + 1]
        })
        break
      }
    }
  },
  
  tapMove: function (e) {
    this.setData({
      scrollTop: this.data.scrollTop + 10
    })
  }
})
```

## 二、swiper

以下代码简单设置了swiper的几个基本属性

属性 | 作用 | 默认值
--- | --- | ---
indicator-dots | 是否显示面板指示点    | false
autoplay       | 是否自动切换         | false
current        | 当前所在滑块的 index | 0
interval       | 自动切换时间间隔      | 5000
duration       | 滑动动画时长         | 500

```
<swiper indicator-dots="true" autoplay="true" current='1' interval='1000' duration='100' bindchange='change'>
  <swiper-item><view class="item bc_green">green</view></swiper-item>
  <swiper-item><view class="item bc_red">red</view></swiper-item>
  <swiper-item><view class="item bc_yellow">yellow</view></swiper-item>
  <swiper-item><view class="item bc_blue">blue</view></swiper-item>
</swiper>
```

```
Page({
  change:function(a) {
    console.log(a)
  }
})
```

以下简单介绍swiper的wxss配置

以上wxml中`<swiper-item>` 组件仅可放置在`<swiper/>`组件中，宽高自动设置为100%。

这里的100%为占满整个swiper，但值得注意的是，实际上若不设置height属性，其显示范围仅仅为文字范围，这里可以为swiper添加一个边框`border` 属性来验证，同时可以验证，滑动时是整个swiper进行滑动，而不是其中的item滑动。

```
swiper {
  height: 200px;
}

swiper-item {
  border: 3px solid black;
}

.item {
  height: 50%;
  width: 50%;
  font-size: 48px;
  color: #fff;
}

.bc_green {
  background-color: green;
}

.bc_red {
  background-color: red;
}

.bc_yellow {
  background-color: yellow;
}

.bc_blue {
  background-color: blue;
}

```

## 一些bug和注意事项：

 - 1.02版本中若用以上方法添加border会将view挤出屏幕，而不是覆盖在view上。
 - swiper组件中，虽然autoplay组件默认值为false，并且其值为布尔类型，但是在1.02版本中只要写上了autoplay属性其值均为true，即使你`<swiper autoplay='a'>` 这样写，swiper仍然会自动播放。

