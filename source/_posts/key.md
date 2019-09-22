日常开发中循环遍历真的是太正常不过的需求了，每个面向框架api开发者不免的都会写过类似的东西：
```jsx
this.state = {
  info: [1, 2, 3, 4, 5],
};

render() {
  const {
    info
  } = this.state;

  return(
    <div>
      {info.map(item => {
        <div>
          <span>{item}</span>
          <input type="text"/>
        </div>
      })}
    </div>
  )
}
```
比如你写了这么一个东西，所渲染的东西是：
1. 根据数组中的元素数量，渲染相对应数量的div
2. 每一个div后面都跟了一个input，用来和各自的div相对应，可以录入一些信息啥的。

由于你实在没力气去搞一个好看的demo，于是这个demo目前长这样：
![image](https://user-images.githubusercontent.com/38184077/65155672-46bcba80-da60-11e9-89dd-4f924714af1b.png)

现在你开开心心地录入了前四条信息，这个demo相应的更新了：
![image](https://user-images.githubusercontent.com/38184077/65155691-4e7c5f00-da60-11e9-8ba4-ca52297537d8.png)


看起来还可以？

好，接着，我们写一个onclick，产品提出的场景是，这个模块可以插入一些最新的信息在顶部区域，类似于压栈的方式。于是你吭哧吭哧的写了如下东西：
```jsx
addInfo(item) {
  const {
    info
  } = this.state
  info.unshift(item)

  this.setState({
    info: info
  })
}
```
大功告成！于是你迫不及待的打算开始自测了，依旧，你录入了四条信息，这个时候打算从顶部插入一条信息进去，你恰了根烟，自信满满的点击了button。

结果是这样的：
![image](https://user-images.githubusercontent.com/38184077/65155702-550ad680-da60-11e9-81f7-6c798f906c02.png)


好像有哪里不对？_(:з」∠)_
找了半天资料，发现了在遍历的时候并没有传入key，于是你稍微改一下代码，在遍历的过程中加入`key`：
```jsx
<div key={`${item}-key`}>
  <span>{item}</span>
  <input type="text"/>
</div>
```
这个时候再试一下，咦？好像好了：
![image](https://user-images.githubusercontent.com/38184077/65155714-589e5d80-da60-11e9-8c4f-254883a74eb4.png)

于是你开心发出了拍肚皮的声音。

讲真，以上真的是一个再简单不过以及日常也会碰到的开发场景了。所以回到正文：

> **他妈的为什么不加key就有bug？**


## why we need keyyyyyyyys in code traversing
看一下react文档中的描述：

> Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity:
```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```
说得挺清楚的了，key主要是用来帮助react进行 **识别** 组件更新/增加/删除，简单而言，react通过对key的识别从而分辨每一个组件在本次更新render的表现。
这样，有了key属性后，就可以与组件建立了一种对应关系，react根据key来决定是销毁重新创建组件还是更新组件。
1. 在存在相同key的情况下，若组件属性有所变化，则react只更新组件对应的属性
2. 在key不同的情况下，会销毁然后创建该组件

说到底，key的作用就是更新组件时 **判断两个节点是否相同**。相同就复用，不相同就删除旧的创建新的。
另，这里说的key其实更类似于数据库中 **主键** 的思维，见过一些代码在遍历的时候把index作为key，是不太好的做法。

## 为什么不推荐使用index作为key
按照官方的说法，key最好是稳定且唯一的字符串。
从上文数组来说，将index作为key会发现bug依旧存在，因为每次render之后，数组更新渲染之后，每个选项的index依旧没变。这种情况下当遇到一些特殊的情况，比如是动态插入数据展示而非静态数组的时候，往往会出现视觉上的问题。

> We don’t recommend using indexes for keys if the order of items may change. This can negatively impact performance and may cause issues with component state. Check out Robin Pokorny’s article for an [in-depth explanation on the negative impacts of using an index as a key.](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) If you choose not to assign an explicit key to list items then React will default to using indexes as keys.
> 
> Here is an [in-depth explanation about why keys are necessary](https://reactjs.org/docs/lists-and-keys.html#keys) if you’re interested in learning more.

## 可以不带key吗？
不使用key的话，组件更新会采用`就地复用`的原则，所以到底是不是用取决于你自己的业务逻辑，如果可以十分之肯定所维护的遍历数组不可能出现以上的场景，或者说可以预见只会出现一些很简单的场景的话，在一个长列表下不使用key也可以，毕竟`就地复用`从性能上来说肯定更优。

但是性能真的差到需要扣key来提升的话考虑不如考虑其他的性能优化方案。