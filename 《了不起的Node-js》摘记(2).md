---
title: 《了不起的Node.js》摘记(2)
date: 2017-08-30 13:46:05
tags: Node.js
categories: Javascript
---
# 序
文章脉络图：
- TCP中数据传递
- HTTP的基础知识和它与TCP的不同
- 中间件

<!-- more -->

## TCP
传输控制协议（TCP）是一个面向连接的协议，它保证了两台计算机之间传输的可靠性和顺序性。

TCP协议对应于TCP/IP体系中的运输层。它向用户提供可靠的端到端的差错和流量控制，保证报文的正确传输

### 面向连接的通讯和保证顺序的传递
通过三次握手建立连接。

通过在IP数据包里添加标志保证顺序的传递

### 面向字节
TCP允许数据以ASCII字符（每个字符一个字节）或者Unicode（每个字符四个字节）
进行传递。

正是因为对消息格式没有严格的约束，使得TCP有很好的灵活性。

### 可靠性
数据发送后，发送放等待确认信息。如果过了预定时间，还没收到回信。
发送方就会重发。

### 流控制
流控制能确保双方之间传输数据的平衡。避免发送方压垮接收方。

### 拥堵控制
- 慢开始
- 拥塞避免

---

## HTTP
超文本传输协议，又称为HTTP，是一种Web协议，它为Web注入
很多强大的功能。他是属于TCP上层的协议

HTTP对应于TCP/IP体系中的应用层，主要解决如何包装数据。它能
识别数据内容。没有它，传输来的数据就失去意义。

### HTTP结构
HTTP协议构建在请求和响应的概念上，对应在Node.js中就是http.
ServerRequest和http.ServerResponse这两个构造器构造出来的
对象。

### 头信息
描述不同的消息内容。

响应文件
```javascript
const http = require('http').createServer(function (req, res) {
	res.writeHead(200,{ 'Content-Type': 'image/png'});
	require('fs').createReadStream('image.png').pipe(res);
});
```

### 连接
TCP服务器和HTTP服务器的实现都调用了createServer方法，并且当客户端连入
时都会执行一个回调函数。但是，它们之间有本质区别，即回调函数中的对象类型。
net 服务器中是个连接（connection）对象，而在HTTp服务器中，则是请求和响应对象。
后者比前者更加抽象。后者的内容更丰富，且提供请求和响应的抽象。

 > 使用up重启HTTP服务器
> 1.  npm install -g up
> 2.  up -watch -port 80 server.js

---

## Connect
Connect是一个基于HTTP服务器的工具集，它提供了一种新的组织代码的方式来与请求
和响应对象进行交互，即中间件。

Connect能帮你封装网络应用中常见的操作。

### 中间件
中间件能单元化地构建代码，并且能够获得高复用性。

中间件本身只是一个函数，有req，res，next这三个参数：
```javascript
//request_time.js
module.exports = function (opts) {
	return (req, res, next) {
		//...
		next();
	};
}；
```
使用中间件：
```javascript
var connect = require('connect'),
		time = require('./request_time');

var server = connect.createServer();

//记录请求的中间件
server.use(connect.logger('dev'));

//时间中间件
server.use(time({ time: 500}));

server.listen(3000);
```
下面介绍些常用的中间件。
#### Static中间件

###### 挂载
static允许将任意一个URL匹配到文件系统中任意一个目录。
```javascript
server.use('/url',connect.static('/public/images'));
```
###### maxAge
static中间件接受一个叫maxAge的选项，这个选项代表一个资源在客户端缓存的时间。
```javascript
server.use('/js',connect.static('/build/',{maxAge: 10000000}));
```
###### hidden
托管以 . 开头的隐藏文件：
```javascript
server.use(connect.static('/res/',{hidden: true}));
```

#### Query中间件
使用了query，就能获取查询字符串这部分的数据
```javascript
server.use(connect.query);
// url: /blog-posts?page=5
server.use((req, res) => {
	//req.query.page
})
```
#### logger中间件
看名字就知道是什么了。

它提供四种日志格式：
- default
- dev
- short
- tiny
```javascript
server.use(connect.logger('dev'));
```
#### body.pares中间件
解析POST请求的信息体,还能处理上传的文件。
```javascript
server.use(connect.bodyParser());
//然乎就可以在req.body中获取信息了
server.use((req, res) => {
	//req.body.myinput
});
```

#### cookie
和query相似，不过它是用来读写cookie。
```javascript
server.use(connect.cookieParser());
//然乎就可以在req.cookies中获取信息了
server.use((req, res) => {
	//req.cookies.secret
});
```
#### 会话（session）
它主要用来记录登录信息，作为多个请求间共享“用户会话”。判断
登录信息还要靠req.body和数据库比较。

#### Redis
Redis是一个既小又快的数据库，有一个connect-redis模块使用
Redis来持久化session数据。

这样就让session驻扎到了Node进程之外。

具体实现：
```javascript
var connect = require('connect'),
		RedisStore = require('connect-redis')(connect);

server.use(connect.session({ store: new RedisStore, secret: 'secret'}));
```
