---
title: 说说最近开发中遇到的几点东西🤔🤔🤔
date: 2019-04-21
---

💃 💃 💃 💃
<!--more-->

算是记录一下踩坑吧

## react从15升16之后带来的一些问题
因为我司前端架构组其实把react之类的一些公共依赖做成了集中式，直接引入集中式文件就行，所以升级的成本对于各条业务线来说其实是很低的，所做的工作也就是在vm文件中替换一下集中式文件的路径，其他的升级成本都被抹平了。

基于这个情况下，升级一下16不是洒洒水吗🤔🤔？
升！
升他妈的！
我要用fiber！

建需求！
改路径！
提测！

咦怎么测试就提bug了呢🤔🤔🤔？

是这样的，我司基于react的基础上再次封装了一套自用的框架，内置一些http的相关处理，在路由切换时`constructor`阶段会注册一些相关的监听器，同时在`componentWillUnMount`时卸载所有监听器。
场景下，在进行页面切换的时候，其实也就是两个组件页面，当进入A页面时，进行接口请求，得到数据渲染页面。当点击跳转b页面，点击返回的时候，这个时候的期望应该是：
1. 回A页面
2. A接口请求数据
3. 得到数据渲染页面

而实际上是：
1. 回页面
2. 接口请求
3. 没了

接口没数据的情况下我肯定不去渲染页面， 所以默认的我们会展示一个骨架屏，导致直接死在这一步。
和同事调研了许久才明白，15其实是基于同步去渲染的，一个组件卸载完毕之后，才会去执行新组件的`constructor`。
 `componentWillUnMount` => `constructor` => `componentWillMount`


16由于fiber的原因，采用的是`async rendering`, 以至于在旧生命周期暂时还能用的时候会变成：
 `constructor（A）` => `componentWillUnMount（B）` => `componentWillMount（A）`

 所以B => A的时候，在A的`constructor`时，绑定了相关http监听，目的是为了触发相关的回调，当绑定成功以后，走到了B的`componentWillUnMount`，这时卸载了页面上所有的http监听，导致当A页面中主接口请求完之后，本身绑定的相关回调函数被清除，展示出来的情况就为，明明接口请求成功但是确不触发相关回调的表现形式。

 详见：
 [codeopen](https://codepen.io/bibi7/pen/axKwNe?editors=1111)




## render之后获取不到ref
这个包括下个其实都是小tips

关于render之后，在`componentDidMount`的时候获取不到ref，主要原因还是在render时候有一个主接口是否返回数据的判断，在回调中会设置一下isDataReady，false的情况下return null
在最初的render中其实接口还没返回数据的时候return null后，`componentDidMount`当然拿不到了，因为根本还没有dom嘛_(:з」∠)_


## appendChild
有个需要不停滚动的需求，在数据大于一条时候无限滚动，同时需要点动效，当时想到的肯定是，append第一个子元素到最下方，同时第一个子元素新增一下class，内置animation，监听animationEnd事件后移除自己即可。不过当真正做起来的时候发现，append了元素后，和设想的表现形式不大一致？🤔🤔
除了append到最后以外，第一个子元素也会直接发生变动，在我添加class之前就变了。
奇怪哦？🤔🤔🤔🤔

调研了一下，`appendChild`方法，当append的元素不在dom树之中时，可以正常append到末尾。
当append的元素本身是一个存在于dom树之中的元素时，`appendChid`表现的形式为替换他们的位置，具体解决方式可以先用`cloneNode`方法处理一下。

撒花✿✿ヽ(°▽°)ノ✿ 💃 💃 💃 💃 💃 💃
