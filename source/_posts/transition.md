---
title: 隐式转换
date: 2019-07-07
---

## js中的隐式转换

关于js在运算中的隐式转换其实还挺坑的，在一些运算符中或者是非全等的条件下很容易发生意想不到的隐式转换。
其中js机制在隐式转换的时候又会涉及到三种抽象操作：
 1. 通过`ToPrimitive()`转换
 2. 通过`ToNumber()`转换
 3. 通过`ToString()`转换

### ToPrimitive(input [, PreferredType])

其中，`ToPrimitive` 该抽象操作接受一个参数input以及可选参数PreferredType，这个参数可以是`Number`或者`String`。主要目的是要把input转化成原始数据类型。

当被标记为`Number`的时候，调用顺序为： valueOf >>> toString，即 valueOf 返回的不是基本数据类型，才会继续调用 toString，如果 toString 返回的还不是基本数据类型，那么抛出错误

当被标记为`String`的时候，调用顺序为： toString >>> valueOf，即 toString 返回的不是基本数据类型，才会继续调用 valueOf，如果 valueOf 返回的还不是基本数据类型，那么抛出错误

如果没有参数，则默认为以下规则：
 1. 如果input类型为Date类型，则PreferredType会被设置为`String`
 2. 其他情况会被设置为`Number`

总结下，也就是除了Date对象以外,其他的PreferredType都是Number

valueOf的转换中，`String`、`Number`、`Boolean`都会被变成相应的原始值，另外`Date`类型会返回日期到现在相对应的毫秒数，初次之外都会返回本身

toString的转换中，`Number`、`Boolean`、`String`、`Array`、`Date`、`RegExp`、`Function`都重写了自己的toString，都会返回相应的字符串形式，其他的toString访问的其实是Object.prototype.toString。同时这个方法也能用来判断实例的类型：
```
const array = new Array();
Object.prototype.toString.call(array); // '[object Array]'
```

#### eg:
```
({} + {})
对象无法直接进行相加，所以进行隐式转换
1. 进行ToPrimitive转换，input就是{}, 这个时候type是Number
2. 先进性valueOf方法，({}).valueOf()返回的还是{}本身，并不是一个原始值，所以继续进行下一步
3. 进行toString方法，({}).toString()返回的是'[object object]'字符串，是原始值，返回
4. 最终就是'[object object]' + '[object object]' = '[object object][object object]'
```

### 关于==的隐式转换
es5的隐式转换文档如下:
```
比较运算 x==y, 其中 x 和 y 是值，返回 true 或者 false。这样的比较按如下方式进行：
1. 若 x 为 null 且 y 为 undefined， 返回 true。
2. 若 x 为 undefined 且 y 为 null， 返回 true。
3. 若 Type(x) 为 Number 且 Type(y) 为 String，返回比较 x == ToNumber(y) 的结果。
4. 若 Type(x) 为 String 且 Type(y) 为 Number，返回比较 ToNumber(x) == y 的结果。
5. 若 Type(x) 为 Boolean， 返回比较 ToNumber(x) == y 的结果。
6. 若 Type(y) 为 Boolean， 返回比较 x == ToNumber(y) 的结果。
7. 若 Type(x) 为 String 或 Number，且 Type(y) 为 Object，返回比较 x == ToPrimitive(y) 的结果。
8. 若 Type(x) 为 Object 且 Type(y) 为 String 或 Number， 返回比较 ToPrimitive(x) == y 的结果。
9. 返回 false。
```
如上可以总结出一些规律：
1. 当时number和string类型互相比较的时候，先把string转换成number
2. 当有布尔值的时候，布尔值转化为number
3. 当有object类型的时候，调用ToPrimitive把object转换成原始值


```
const a = {
  i: 1,
  toString: function () {
    return a.i++;
  }
}
if (a == 1 && a == 2 && a == 3) {
  console.log('hello world!');
}

a == 1 会先用ToPrimitive隐式转换a，在经过valueOf之后返回本身，继续调用toString。
这个时候由于toString被重写的原因，返回a.i++, 所以2 == 2 为true，后续同理
```
