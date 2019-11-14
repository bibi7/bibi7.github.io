---
title: scheduleWork
date: 2019-11-14
---

<!--more-->
偷一张讲fiber必定会看到的网图：
![image](https://user-images.githubusercontent.com/38184077/68594549-c0a67880-04d2-11ea-854e-35d88d18105d.png)
不同于15的同步渲染，一旦组件调用嵌套过于深入的话，绘制一个组件可能会过长了阻塞渲染引擎，从而带给我们十分糟糕的体验。
16变化了啥就不多讲了。主要看一下调度这东西具体的思路

### **那么必然就来要认识一个新api了**：requestIdleCallback
[requestIdleCallback](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback)照例搬运mdn。

> window.requestIdleCallback()方法将在浏览器的空闲时段内调用的函数排队。这使开发人员能够在主事件循环上执行后台和低优先级工作，而不会影响延迟关键事件，如动画和输入响应。函数一般会按先进先调用的顺序执行，然而，如果回调函数指定了执行超时时间timeout，则有可能为了在超时前执行函数而打乱执行顺序。
> 
> 您可以在空闲回调函数中调用requestIdleCallback()，以便在下一次通过事件循环之前调度另一个回调。

该回调函数接收一个IdleDeadline对象作为入参。其中IdleDeadline对象包含：
1. `timeRemaining()`，表示当前帧剩余的时间，也可理解为留给任务的时间还有多少。
2. `didTimeout`，布尔值，表示任务是否超时，结合 `timeRemaining` 使用。

上文说过，render会计算两个时间，一个是开始时间，另一个为过期时间。其中过期时间又可以这么理解
过期时间 = 开始时间 + 一个权重常量时间

优先级常量：
![image](https://user-images.githubusercontent.com/38184077/68595699-ecc2f900-04d4-11ea-85ed-a7d6e5817495.png)

相对应的时间设置，很奇怪这个文件只找到了两个：
![image](https://user-images.githubusercontent.com/38184077/68595915-53e0ad80-04d5-11ea-94f1-1454fd7cf72f.png)
![image](https://user-images.githubusercontent.com/38184077/68595943-5e9b4280-04d5-11ea-9f64-24a79761b424.png)

中间的计算过程不管，最终得出了一个过期时间。


看完`requestIdleCallback`的mdn解释后，有了一个大概的印象。这东西其实就类似于一个浏览器空余时候才执行的回调。支持传入timeout，在时间超过了timeout的时候强制执行回调。

嗷！听起来好像和调度有点类似？
调度其实十分依赖`requestIdleCallback`，但是原生的浏览器有个十分硬伤。`requestIdleCallback`一秒内的回调至多只有20次。

> requestIdleCallback is called only 20 times per second - Chrome on my 6x2 core Linux machine, it's not really useful for UI work.

gg，咋办？自己造轮子啊！

### requestAnimationFrame
又遇见这个b了，我记得以前还写过类似的。[连接](https://github.com/bibi7/fe-daily-increase/issues/19)
再次总结一下几个注意的点：
1. requestAnimationFrame 方法不同与 setTimeout 或 setInterval，它是由系统来决定回调函数的执行时机的，会请求浏览器在下一次重新渲染之前执行回调函数。并且是跟着显示器刷新率走的。

2. 一帧中只会执行一次


### 一帧中发生了啥
![image](https://user-images.githubusercontent.com/38184077/68599473-96a58400-04db-11ea-89d6-eefdee21650b.png)
通过上图可看到，一帧内需要完成如下六个步骤的任务：
1. 处理用户的交互
2. JS 解析执行
3. 帧开始。窗口尺寸变更，页面滚去等的处理
4. requestAnimationFrame(rAF)
5. 布局
6. 绘制

现代显示器刷新率各不相同，标准60或者电竞144都有。如果按照60帧来算的话，一秒也就是1000 / 60 = 16.6ms/frame

如果这一帧的时间内，处理的东西超过了16毫秒，那么从理论上就会造成卡顿，无非是可感知的区别与否。
如果以上操作所花的时间没有超过这个阈值的话，那么我就可以理解为：

**当前帧可以执行一些多余的操作。**

假设当前时间为 5000，浏览器支持 60 帧，那么 1 帧近似 16 毫秒，那么就会计算出下一帧时间为 5016。
得出下一帧时间以后，我们只需对比当前时间是否小于下一帧时间即可，这样就能清楚地知道是否还有空闲时间去执行任务。

在事件循环中，最先执行的就是宏任务，但是生成宏任务也有优先级，可以选择优先级最高的`MessageChannel `（并没有用过）

那么react手动造的这个requestIdleCallback的轮子，大致可以这么看：

> requestAnimationFrame + 计算帧时间及下一帧时间 + MessageChannel


回到[上一篇](https://github.com/bibi7/react-read/issues/3)的结尾，开始scheduleWork调度

```js
function scheduleWork(fiber: Fiber, expirationTime: ExpirationTime) {
  // 获取FiberRoot
  const root = scheduleWorkToRoot(fiber, expirationTime);
  if (root === null) {
    return;
  }

  // 这个分支表示高优先级任务打断低优先级任务
  // 这种情况发生于以下场景：有一个优先级较低的任务（必然是异步任务）没有执行完，
  // 执行权交给了浏览器，这个时候有一个新的高优先级任务进来了
  // 这时候需要去执行高优先级任务，所以需要打断低优先级任务
  if (
    !isWorking &&
    nextRenderExpirationTime !== NoWork &&
    expirationTime < nextRenderExpirationTime
  ) {
    // 记录被谁打断的
    interruptedBy = fiber;
    // 重置 stack
    resetStack();
  }
  // ......
  if (
    // If we're in the render phase, we don't need to schedule this root
    // for an update, because we'll do it before we exit...
    !isWorking ||
    isCommitting ||
    // ...unless this is a different root than the one we're rendering.
    nextRoot !== root
  ) {
    const rootExpirationTime = root.expirationTime;
    // 请求任务
    requestWork(root, rootExpirationTime);
  }

  // 在某些生命周期函数中 setState 会造成无限循环
  // 这里是告知你的代码触发无限循环了
  if (nestedUpdateCount > NESTED_UPDATE_LIMIT) {
    // Reset this back to zero so subsequent updates don't throw.
    nestedUpdateCount = 0;
    invariant(
      false,
      'Maximum update depth exceeded. This can happen when a ' +
        'component repeatedly calls setState inside ' +
        'componentWillUpdate or componentDidUpdate. React limits ' +
        'the number of nested updates to prevent infinite loops.',
    );
  }
}
```
这里一共做了几个事情：
1. `scheduleWorkToRoot`函数内部通过while循环的方式return出最顶部的root，找的时候会不停地设置每一个fiber的childExpirationTime，提高优先级

2. 如果有更高的优先级，则记录被打断的fiber并且通过`resetStack`重置低优先级任务，并且取消造成的影响（具体实现没看）。

3. `requestWork `开始请求任务，理解为开始进行调度的下一个工作吧。