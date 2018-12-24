---
title: background-attachment
date: 2018-12-24
---

大半个月了，再来说点其他的css

`background-attachment`

<!--more-->

## wow
其实主要还是前几天做业务的时候遇到了滚动视差的需求，经过同事提（剧）醒（透），发现了这么一个好玩的东西。
其实以前也实现过，只不过用的是js的方式来弄的。知道能用纯css的方式实现还是稍微诧异了一下。

`background-attachment`主要用来设置背景图像是否固定或者随着页面的其余部分滚动，默认值为scroll，有这么几种用法（其实就一种）：

`scroll`:默认值。背景图像会随着页面其余部分的滚动而移动。
`local`:相对于元素内容固定，但如果元素本身有可滚动因素，比如overflow，背景将会随着内容滚动
`fixed`:当页面的其余部分滚动时，背景图像不会移动。


## 栗子

有这么一个场景 **like this ↓**：
![text](/imgs/attach1.gif)



加上了之后 **like this ↓**：
![text](/imgs/attach2.gif)


这一部分的代码简单的很，就不贴了。（其实是今天太冷了不是很想打太多字嘻嘻😉
周六看看补充点其他的高级用法吧（😉











<font face="黑体" size="2px" color="#a6a6a6">再来一弹css</font>
