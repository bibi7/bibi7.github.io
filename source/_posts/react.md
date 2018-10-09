---
title: react
date: 2018-10-08
---


算是一点个人笔记吧
看到觉得不妥之处的话随时欢迎提issue或者pr

<!--more-->

### 起手
我本以为我短时间内不会学react的，毕竟我是那么的深爱vue（你给我住口！
在上一篇还是在为vue提pr，这一篇立马变成react入门的情况下好像并没有太大的说服力，可是陆金所真的没有vueQAQ

为了不被公司赶出去，行叭！学就学叭！

占个坑，待续。


## 几种创建react组件的方式
1. 函数式的定义的无状态组件
2. ```React.creatClass```
3. ```extends React.Component```

第一种，简单的无状态组件：
```javascript
function Hello() {
  return <div>`hello${props.name}`</div>
}
```
ReactDOM.render(<Hello name="bibi"/>, mountNode)
无状态组件一般为纯展示用组件，不涉及到state以及交互，无状态的函数式组件只根据传入的props进行展示。
**只要有可能，尽可能地使用无状态组件**

第二种，creatClass方式：

待续。

<!--
## state和props
## 生命周期
## 有状态、无状态组件
## redux
-->
