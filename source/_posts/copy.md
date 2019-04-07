---
title: js中的深浅拷贝
date: 2019-02-21
---

发现好久不大更新
实属太懒的原因（。
可不能这样_(:з」∠)_ ， 干脆写篇深浅拷贝的总结好了

<!--more-->


## 关于变量的复制
都知道，js中有基本数据类型以及引用数据类型，关于他们的复制如下：
```js
// 基本数据类型
const first = 'first'
let second = first
second = 'second'

console.log(first)   // first
console.log(second)  // second
```

看起来挺符合直觉的， 看一看引用数据类型的复制
```js
// 引用数据类型
const objFirst = {
  name: 'bibi',
  age: 23
}
const objSecond = objFirst
objSecond.age = 18

console.log(objFirst)  // {name: 'bibi', age: 18}
console.log(objSecond) // {name: 'bibi', age: 18}
```
基础不怎么扎实的同学（说的我好像很扎实一样☹️）可能就会疑惑为啥只是改变的objSecond的age属性，同样的却会作用在objFirst对象上，这不是明明复制了一个新对象了嘛。
有点点奇怪噢🤔🤔🤔

这里就要谈到栈和堆了，也就是所谓的stack和heap
是这样的，基本数据类型的存储是存储在栈之中，栈之中的数据大小确定，自动分配内存空间，适合存储简单的数据段，当你进行复制操作的时候，栈会新开辟一块内存，名为key，值为你复制过来的值。
引用就有点不一样了，引用数据类型存储在堆之中，动态分配内存，大小不确定也不会自动释放。当你进行引用数据类型复制的时候，注意，在栈中依然会开辟一块内存，名为key，但要注意值是一个指向真实存放于堆内存中的值的一个**地址**
当你进行复制操作的时候，复制过来的其实为地址，objSecond和objFirst实际上依然是同一个对象。

说到底两者的复制就是传值和传址的区别，而浅拷贝实际上指的就是如上，两个变量实际上指向一个对象的情况。🙄


## 深拷贝
说一下思路，新建一个空对象，遍历需要复制的对象，遇到基本数据类型直接进行复制，遇到引用数据类型，因为一般引用类型最终存储的还是基本类型，那就递归然后再遍历

```js
const deepCopy = (obj) => {
  let newObj = Array.isArray(obj) ? [] : {}
  if (obj && typeof obj === "object") {
    for(let key in obj) {
      if (Object.hasOwnProperty(key)) { // for in会遍历所有可枚举属性，包括原型上的
        if (obj[key] && typeof obj[key] === 'object') {
          newObj[key] = deepCopy(obj[key])
        } else {
          newObj[key] = obj[key]
        }
      }
    }
  }
  return newObj
}
```

## 奇淫技巧
```js
JSON.parse(JSON.stringify(obj))
```
就很简单粗暴了🤔

<font face="黑体" size="2px" color="#a6a6a6">🤔🤔这表情真好玩🤔🤔</font>
