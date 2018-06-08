---
title: 柯里化(Curring)
date: 2018-06-08
---
谈到这个就有点头大。。
其实我还是蛮惧怕柯里化的，感觉就像大山一样，不过总要跨过去的是吧(╥╯^╰╥)
> <font face="黑体" size="2px" color="#a6a6a6">柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数(最初函数的第一个参数)的函数，并且返回接受余下的参数且返回结果的新函数的技术。</font>

说得简单点：

> <font face="黑体" size="2px" color="#a6a6a6">只传递函数一部分的参数来调用他，并且返回一个函数去处理剩下的参数，也叫作部分求值</font>


还是举一个老掉牙的栗子

```javascript
function add(a, b, c) {
    return a + b + c;
}
```

那么这个时候add的柯里化_add可以为：
```javascript
function _add(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        }
    }
}
```

因此以下两个运算都是等价的：
```javascript
add(1, 2, 3)

_add(1)(2)(3)
```


简单来说，柯里化其实就是一个**参数收集**的过程，将每一次传入的参数收集起来，然后在最里层再进行处理。

接下来自己手动实现一个简单的柯里化累加。


```javascript
const curring = function () {
    //保存所有的参数
    let _args = [];

    return function fn() { //这里之所以多了fn是因为避免使用argument.callee
        //没有args，表明没有参数了
        if (arguments.length === 0) {
            return _args.reduce((a, b) => a + b)
        }

        //继续存入参数
        _args.push(...arguments);
        return fn;
   }
};
const add = curring();

const add = curring();


//下面三者等价
add(1, 2)(3, 4)(5); //15
add(1, 2, 3)(4, 5); //15
add(1)(2)(3)(4)(5); //15
```
完工！⁄(⁄ ⁄•⁄ω⁄•⁄ ⁄)⁄


这篇blog算是很简单的，不能够特别明显的凸显柯里化的优势，主要目的还是初识一下柯里化本身。


当然，柯里化通用式具备更加强大的能力，我们靠眼力自己封装的柯里化函数则自由度偏低，柯里化函数的运行过程其实是一个参数的收集过程，我们将每一次传入的参数收集起来，并在最里层里面处理。所以借助这个方式可以总结出一份柯里化的通用式。
这里借用梧桐大大的currying通用式：
```javascript
// 简单实现，参数只能从右到左传递
function createCurry(func, args) {

    var arity = func.length;  //函数的length表示形参个数
    var args = args || [];

    return function() {
        var _args = [].slice.call(arguments);
        [].push.apply(_args, args);

        // 如果参数个数小于最初的func.length，则递归调用，继续收集参数
        if (_args.length < arity) {
            return createCurry.call(this, func, _args);
        }

        // 参数收集完毕，则执行func
        return func.apply(this, _args);
    }
}


function check(targetString, reg) {
    return reg.test(targetString);
}


var _check = createCurry(check);

var checkPhone = _check(/^1[34578]\d{9}$/);
var checkEmail = _check(/^(\w)+(\.\w+)*@(\w)+((\.\w+)+)$/);


checkPhone('183888888');
checkEmail('xxxxx@test.com');
```


<font face="黑体" size="2px" color="#a6a6a6">明天就是周六了</font>
