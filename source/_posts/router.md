---
title: 简易react路由
date: 2020-01-14
---


### 前端路由
路由是spa应用很强大的一个地方，可以在一个页面通过不同的url展示不一样的内容，不管是vue还是react，实际上都是根据hash和history的特性来实现的路由系统，他们的最大的特性，都是可以通过改变url的同时不刷新页面展示。

### hash
从以前古老的a标签增加锚点定位到页面上的某一个位置，其实就是最开始常用的用法。比如：
```html
<a href="#/someWhere"></a>  

<!--比如：//https://github.com/bibi7/fe-daily-increase/issues/#/someWhere-->
```
可以采用`onhashchange`的方式监听hash变化

### history
`history`同样也可以用来进行url的增改，不过可能语法上有点不一样。一般来说常用的有：
```js
history.pushState({}, null, path)
history.replaceState({}, null, path);
```
和hash不同的是，history触发的url变化没办法监听到。另外，二者在进行浏览器前进后退的时候，都可以采用监听`popstate`的方式。


### 实现一个基于react的简易版本：
```jsx
//思路：
//页面进去的时候，初始化所有的router，维护一个实例队列
//通过页面上的组件点击触发路由事件，实例队列全部调用更新，判断是否要展示组件
//监听popstate事件，调用队列更新
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
const routeInstance = [];
const register = (comp) => routeInstance.push(comp)
const unRegister = (comp) => routeInstance.splice(routeInstance.indexOf(comp), 1)

const Party = () => 
    <div>
        <h2>Party</h2>
    </div>

const School = () => 
    <div>
        <h2>School</h2>
    </div>

const Home = () => 
    <div>
        <h2>Home</h2>
    </div>

const forward = (path) => {
    history.pushState({}, null, path)
    routeInstance.forEach(item => item.forceUpdate())
}

const match = (pathname, option) => {
    const { path } = option;
    
    return new RegExp(`^${path}`).test(pathname)
}

window.addEventListener('popstate', () => {
    routeInstance.forEach(item => item.forceUpdate())
})

class Route extends Component {
    constructor(props) {
        super(props)
        register(this)
    }

    componentWillUnmount() {
        unRegister(this)
    }

    render() {
        const {
            path,
            component
        } = this.props
        const isMatch = match(window.location.pathname, { path, })
        if (!isMatch) return null

        if (component) {
            return React.createElement(component);
        }
    }
}



const App = () => {
    return (
        <div>
            <ul>
                <li>
                   <button  onClick={() => {forward('home')}}>go home</button>
                </li>
                <li>
                    <button onClick={() => {forward('school')}}>go school</button>
                </li>
                <li>
                    <button onClick={() => {forward('party')}}>go party</button>
                </li>
            </ul>
            <Route path="/party" component={Party}/>
            <Route path="/school" component={School}/>
            <Route path="/home" component={Home}/>
        </div>
    )  
}

ReactDOM.render(<App />, document.querySelector('#root'))
```

简易版效果：
![GIF](https://user-images.githubusercontent.com/38184077/72221788-d13da280-3598-11ea-8d1e-a8ebbdb249b9.gif)


参考：
[前端路由实现与 react-router 源码分析](https://github.com/joeyguo/blog/issues/2)