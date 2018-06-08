---
title: websocket
date: 2018-06-07
---
这两天接了个电面，感觉自己发挥的还是不太行，谈到了websocket和currying，这两个是真的不会。。平时没用上所以也很少去接触，还是需要抓一把，多充点电。
从websocket开始吧，并把一些东西记录起来。

websocket与http一样，都是通信协议，相比于http来说，websocket带来了不一样的东西。

从前的http，每一次的请求都是由客户端发起的，并且**只能**由客户端发起，http无法做到服务端主动进行推送信息。
从这点来说，http是十分麻烦的，因为一旦需求中遇到了要不断地接受服务器传递的信息的时候，只能用轮询来进行操作，就很僵硬。（突然想到了EventSource）

> <font face="黑体" size="2px" color="#a6a6a6">后来诞生了websocket</font>

websocket最大的特点就是：**服务器可以主动向客户端推送消息，客户端也同样主动向服务器发送信息**

本质上来说，websocket是基于tcp，在发起连接的时候通过http/https发起一条特殊的http请求后创建一个用于交换数据的tcp连接。此后客户端和服务器通过此连接来进行实时通信。

带来的好处显而易见，减少了无意义的重复请求使得资源开销更低的同时，服务端主动推送也让通信也更高效。

## 简单的示例
```javascript
const ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = (evt) => {
  console.log("Connection open ...");
  ws.send("Hello WebSockets!");
};

ws.onmessage = (evt) => {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = (evt) => {
  console.log("Connection closed.");
};   
```

## 相关的api

`webSocket.readyState`可用来判断连接的状态，一般处于一下四种之一：
1. 值为0，表示正在连接
2. 值为1，表示连接成功，可通信
3. 值为2，表示连接正在关闭
4. 值为3，表示连接已经关闭，或者无法打开

`WebSocket.onopen`用于指定连接成功后所执行的回调
`WebSocket.onclose`用于指定连接关闭后所执行的回调
`WebSocket.onmessage`用于指定收到服务器数据后所执行的回调
`WebSocket.onerror`用于指定报错时所执行的回调
`WebSocket.send`用于向服务器发送数据
```javascript
ws.send('send some message')
```
实例对象的`bufferedAmount`属性用来表示还剩余多少的二进制数据尚未发送，可用来判断发送是否结束。
```javascript
const data = new ArrayBuffer(1000000);
ws.send(data);

if (ws.bufferedAmount === 0) {
    console.log('字节发送完毕')
}
```
注意：服务端发送过来的数据可能是文本，也可能是二进制数据（`blog`或者`Arraybuffer`），可以使用`binaryType`显示的指定收到的二进制数据类型


<br>
<font face="黑体" size="2px" color="#a6a6a6">哎面试没过</font>
