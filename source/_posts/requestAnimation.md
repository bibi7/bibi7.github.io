---
title: requestAnimationFrame
date: 2019-08-11
---

> 去年在用vue写玩具的时候遇到了toast相关需求，[链接自取](https://bibi7.github.io/2018/12/02/restartAnimation/)，当时也没怎么考虑直接做了个组件的形式。

主要的卡点在于多次触发animation，清空上一次未完成的animation。
虽然当时觉得实在太蠢后期优化，但是由于是在太懒的缘故，这篇hape代码现在还挂在blog上。。。🌚🌚
都说自己的代码只有自己能看得懂，但是当我好几个月后再回来瞅一下还是觉得：
这也太难懂了。。🤔相对于一个toast真的没必要这么绕🐴


算了不拖延了，赶紧搞个toast2.0🌝

### toast2.0
这东西其实不应该做成类似组件的方式，最好还是做成工具类相关（再次对不起我第一次真的没有细想很多），整个成一个utils是坠好的！

期望：
```js
import {
  toast
} from 'xxx/utils'

toast('some info')
```


开工了开工了！比如有以下一个toast的div：
```html
<div id="toast" class="toast" @animationend="toastEnd"></div>
```
animation事件结束后隐藏自己
```js
toastEnd() {
  let toast = document.querySelector('#toast')
  toast.classList.remove('show')
}
```

对外抛出toast工具函数
```js
export function toast(msg) {
  let toast = document.querySelector('#toast')
  toast.innerHTML = msg;
  document.querySelector("#toast").className = "toast";
  document.querySelector("#toast").className = "toast show";
}
```

toast函数中本来的设想就是，取消show的激活状态，这样display重新为none了，然后再次添加show的class，置于show中的animation属性就会再次出发，但是实测不太行。。。
查了一圈，发现都是基于setTimeout来实现，怎么说呢，有点浮于表面的解决方法。
看的比较多的几种：
1. 重载html元素
2. 切换css样式
3. 写一个一模一样的动画（这不就是那个hape组件的做法吗
4. `webkitAnimationPlayState`，讲真不是很了解

继续找一下有没有别的实现？🤔🤔

找了一圈，终于找到了看起来能从根源上解决的api：`window.requestAnimationFrame`

我们来瞅瞅MDN的解释：

> window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

看起来okk，就是他了！
```js
export function toast(msg) {
  let toast = document.querySelector('#toast')
  toast.innerHTML = msg;
  document.querySelector("#toast").className = "toast";
  window.requestAnimationFrame(() => {
    document.querySelector("#toast").className = "toast show";
  });
}
```
来说一下主要涉及到的步骤：
1. `document.querySelector("#toast").className = "toast"`，去除当前动画
2. `window.requestAnimationFrame`告诉浏览器，我希望执行一次动画啦！赶紧给窝重绘
3. 在重绘的回调中再次添加动画
4. 然后浏览器**重新绘制了页面**

看到一个其他的类似方案，在两次class的增删中添加如下：
```js
document.querySelector("#toast").className = "toast"

//重新设置一次offsetWidth
element.offsetWidth = element.offsetWidth

document.querySelector("#toast").className = "toast show"
```

其实和`requestAnimationFrame`原理是一样的，根本原因是，一个animation只会在挂载到了dom上后触发一次，如何激活第二次甚至多次，实质上你要考虑的其实就是：

> 你要如何让这个dom发生**重绘**或者**重排**

`offsetWidth`发生了重绘，所以重新计算后触发animation，`requestAnimationFrame`手动触发了一次重绘。包括remove节点后再次append，其实也就是触发一次重排。

当然，这个api主要还是逐帧调用，每秒60次回调从而实现一些比较复杂的自定义动画。回调函数执行次数通常与浏览器刷新率一致，而且性能优秀。当成一个主动触发重绘的语法糖毕竟用途比较少。
