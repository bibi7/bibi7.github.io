---
title: 柯里化(Curring)
date: 2018-06-08
---
谈到这个就有点头大。。

> <font face="黑体" size="2px" color="#a6a6a6">柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数(最初函数的第一个参数)的函数，并且返回接受余下的参数且返回结果的新函数的技术。</font>

说得简单点：

> <font face="黑体" size="2px" color="#a6a6a6">只传递函数一部分的参数来调用他，并且返回一个函数去处理剩下的参数，也叫作部分求值</font>


还是举一个老掉牙的栗子

```
function add(a, b, c) {
    return a + b + c;
}
```

那么这个时候add的柯里化_add可以为：
```
function _add(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        }
    }
}
```

因此以下两个运算都是等价的：
```
add(1, 2, 3)

_add(1)(2)(3)
```


简单来说，柯里化其实就是一个**参数收集**的过程，将每一次传入的参数收集起来，然后在最里层再进行处理。

接下来我们可以手动实现一个简单的柯里化累加。


```
const curring = function () {
    //保存所有的参数
    let _args = [];

    return function () {
        //没有args，表明没有参数了
        if (arguments.length === 0) {
            return _args.reduce((a, b) => a + b)
        }

        //继续存入参数
        _args.push(...arguments);
        return arguments.callee;
   }
};


const add = curring();


//下面三者等价
add(1, 2)(3, 4)(5); //15
add(1, 2, 3)(4, 5); //15
add(1)(2)(3)(4)(5); //15
```

当然，上面只是一个add的柯里化，并没有传入函数。所以接下来可以实现一个



<font face="黑体" size="2px" color="#a6a6a6">严格模式下`arguments.callee`禁用，头疼</font>