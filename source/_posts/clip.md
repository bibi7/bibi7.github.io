---
title: background-clip
date: 2018-10-27
---

今天来聊一点点css

一个小魔术
<!--more-->


`background-clip`，有很多人估计用过，他有三个值，分别是：
1. `boder-box`
2. `padding-box`
3. `content-box`

估计很多人都用过？其实我觉得有点类似于`box-sizing`
今天要聊的是另一个值：`text`！

`background-clip: text`：从前景内容的形状（比如文字）作为裁剪区域向外裁剪，如此即可实现使用背景作为填充色之类的遮罩效果


## 干说有点看不懂，上点干货


```html
<div class="b_co">
  <p>come text</p>
</div>
```

```css
.b_co {
  text-align: left;
  margin: 150px auto;
  width: 400px;
  height: 200px;
  background: url('./s86u.jpg') no-repeat center center;
  background-size: 100% 100%;
  padding: 50px;
}

.b_co p {
  line-height: 200px;
  font-size: 50px;
  color: red;
}
```



<br>  
**like this ↓**
![text](/imgs/clip_01.jpg)


接下来窝要开始变魔术了。
```css
.b_co {
  text-align: left;
  margin: 150px auto;
  width: 400px;
  height: 200px;
  background: url('./s86u.jpg') no-repeat center center;
  background-size: 100% 100%;
  padding: 50px;
  -webkit-background-clip: text;
}

.b_co p {
  line-height: 200px;
  font-size: 80px;
  color: transparent;
}
```

<br>  
**like this ↓**
![text](/imgs/clip_02.jpg)

**是！不！是！很！麻！吉！克！**
**这可太帅了叭！**

简而言之，background-clip可以将区域按照内容文字进行裁剪，当我们把文字设置为透明的时候，就可以凭借着背景图片展现出一种类似于艺术字的效果。
（*另外不要问我为什么文字居左，因为右边他妈的都是白底*
<br>  
<br>  
## 同理实现文字渐变
```css
.b_co {
  text-align: left;
  margin: 150px auto;
  width: 400px;
  background: linear-gradient(to bottom, #00A09A, #00FA9A);
  padding: 50px;
  -webkit-background-clip: text;
}

.b_co p {
  font-size: 80px;
  color: transparent;
}
```
<br>  
**like this ↓**
![text](/imgs/clip_03.jpg)
<br>  
<br>  
## 另外来点比较高级的，我们做个毛玻璃放大镜。

```css
.b_co {
    position: relative;
    margin: 0 auto;
    width: 100%;
    height: 100%;
    overflow: hidden;
}

.mask {
    width: 100%;
    height: 100%;
    background: url(./s86u.jpg) no-repeat center center;
    background-size: cover;
    filter: blur(10px);
    transition: .3s;
}

.b_co p {
    position: absolute;
    top: 0%;
    left: 0;
    width: 100%;
    height: 100%;
    text-align: center;
    background: url(./s86u.jpg) no-repeat center center;
    background-size: cover;
    -webkit-background-clip: text;
    color: transparent;
    animation: move 4s ease-in infinite alternate;
}

@keyframes move {
    0% {
        line-height: 100px;
        font-size: 100px;
    }
    100% {
        line-height: 600px;
        font-size: 1500px;
    }
}
```
<br>
**like this ↓**
![text](/imgs/GIF.gif)


<br>
大概就是这样，不说了我要去看s8了！ig加油鸭！！！

<font face="黑体" size="2px" color="#a6a6a6">我是不是很久没写过css相关的东西了？</font>
