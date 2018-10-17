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



## 几种创建react组件的方式
1. 函数式的定义的无状态组件
2. `React.creatClass`
3. `extends React.Component`

第一种，简单的无状态组件：
```javascript
function Hello() {
  return <div>`hello${props.name}`</div>
}
ReactDOM.render(<Hello name="bibi" />, mountNode)
```
无状态组件一般为纯展示用组件，不涉及到state以及交互，无状态的函数式组件只根据传入的props进行展示。
**只要有可能，尽可能地使用无状态组件**

第二种，creatClass方式：
```javascript
var Hello = React.creatClass({
  getInitialState: function() {
    return {
      name: 'bibi'
    }
  }
  render: function() {
    return <div>`hello${this.name}`</div>
  }
})
```

第三种，es6的calss方式
```javascript
class Hello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'bibi'
    }
  }
  render() {
    return <div>hello, {this.name}</div>  
  }
}
```
二和三被称为有状态组件，有着内部的状态`state`,放在`getInitialState`中，以`class`创建的方式，`state`直接写在`constructor`内即可。
现今开发中最常用为第三种，同时react组件必须 **大写开头**


## 生命周期

![react](/imgs/React-life-cycle.jpg)
1. `getDefaultProps()` 设置默认的props
2. `getInitialState()` 获取自身组件的state，es6中直接放到了`constructor`定义state，使用`this.state`就可以访问。
3. `componentWillMount()` 在挂载前触发，一般来说只会触发一次，可以在这里访问state。
4. `render()` 创建虚拟dom，进行diff算法，而且更新dom树也采用此方法，在挂载后更新`state`也会重新触发此方法。
5. `componentDidMount()` 挂载后调用。
6. `componentWillReceiveProps(nextProps)` 在获得新的`props`时候调用，可以访问到此时的`props`和新的`props`。
7. `shouldComponentUpdate(nextProps, nextState)` 组件接受新的state或者props时调用，返回false时会阻止更新
8. `componentWillUpdata(nextProps, nextState)` 组件即将更新时调用。
9. `componentDidUpdate()` 完成更新时调用。
10. `componentWillUnmount()` 即将卸载时调用。
<!--## state和props-->
