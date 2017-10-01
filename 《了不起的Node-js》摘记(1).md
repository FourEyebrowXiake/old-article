---
title: 《了不起的Node.js》摘记(1)
date: 2017-08-29 14:01:07
tags: Node.js
categories: Javascript
---
参考链接：[Node 中的流（Stream)](https://segmentfault.com/a/1190000000357044)

# 序
看标题就知道是《了不起的Node.js》的读书笔记。
在阅读的过程中会提取自己认为的重点，并简要地概括下它是什么，又怎么用。

书中有涉及不少网络方面的知识。正巧上学期刚上完计算机网络的课，脑袋里还有基本的概念。期末复习时，特地做了一张思维导图。看书的时候时常拿来参考。果然，好记性不如烂笔头，要记笔记，时常做总结。

Stream是Node.js中非常重要的API。但是书中的介绍有点少，而且还比较老。所以，就自己补充了一下。还有书中关于Event Loop的讲解，理解起来让人吃力。推荐大家看一下阮一峰老师的文章。

<!-- more -->

## Javascript概览
### V8中的javascript
#### 获取对象上所用自建键
```javascript
var obj= { a:'a', b:'b' };
Object.keys(obj);//['a', 'b']
```
#### 为函数命名
v8能在显示堆栈信息时，将函数名显示出来，如果函数进行命名了话。
#### _PROTO_
使得定义继承变得更加容易：
```javascript
function Animal () {}
function Ferret() {}
Ferret.prototype._proto_ =Animal.prototype;
```
#### 存取器
访问属性使过__defineGetter__、设置属性使用__defineSetter__。
```javascript
Date.prototype.__defineGetter__('name',function(){});
```
---

## 阻塞与非阻塞IO

### Event Loop
原书内容不好理解，可以看阮一峰老师的文章。
[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

在基础架构上Node采用一个长期运行的进程。当客户端再次发送请求时，Node的进程不会初始化。所以，作用域的变量会保存修改。

Node通过使用回调函数来创建非阻塞的程序。所以在Node.js的异步编程，要使用回调函数。

Node使用Event Loop。Event Loop就是不断循环Node的主线程，处理非阻塞的程序。在Node的主线程里是不推荐放阻塞程序的。在一次Event Loop时， Event Loop都会查一下callback queue,看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

Node是单线程，所以有些情况下不遵守setTimeout设定的时间。setTimeout(fn,0)的含义是，指定某个任务在主线程最早可得的空闲时间执行，也就是说，尽可能早得执行。它在"任务队列"的尾部添加一个事件，因此要等到同步任务和"任务队列"现有的事件都处理完，才会得到执行。

### 错误处理
Node对待错误很严格。默认情况下，如果错误没有捕获，进程就会崩溃。

### 堆栈追踪
采用回调函数时，因为无法捕获一个未来才会执行到的函数所抛出的错误，所以，堆栈信息显示的是从事件轮询开始的。这就是为什么在Node.js中，每步都要正确处理的原因。一旦遗漏，错误很难被追踪，因为上下文信息都丢失了。

---

## Node中的JavaScript
### global全局对象
- global: 和浏览器上的window一样。任何global对象上的属性都可以被全局访问。
- process: 所用全局执行的上下文内容都在process对象中。
### 实用全局对象
console，setTimeout,nextTick(相当于setTimeout(fn,1))
### 模块系统
三个核心概念：
- require
- module
- exports
module.exports是一个对象。你可以重写它。它决定require获得什么。node通过module.exports来暴露API。

### buffer
buffer是一个表示固定内存分配的全局对象。它就好比一个由8位字节元素组成的数组，可以有效地处理二进制数据。

---

## 命令行工具
### 常用API
- fs.readdir(file_path,callback());
- fs.readFile(file_path,'utf8',callback());
- fs.stat(file_path, callback()); 文件或目录下的元元素
- process.stdin.resume()； 等待输入
- process.stdin.setEncoding()；
- process.stdin.pause(); 暂停流
- process.stdin.on('event'，callback())； 等待输入
- process.stdout.write();
- process.argv.slice(2); 获取真正的元素
- process.cwd(); 获取当前目录
- process.chdir('/'); 灵活地更改目录
- process.env变量来轻松访问shell环境下的变量
- process.env.NODE_ENV 判断是运行在开发环境下，还是产品模式下。

### ANSI转义码
为文本添加颜色：
```javascript
console.log('\033[90m' +data.replace(/(.*)/g,'  $1')+'\033[39m');
```
- \033表示转义序列开始
- [表示开始颜色设置
- 90表示前景色是亮灰色
- m表示掩饰设置结束
- 最后用将颜色设置回去

### 老的Stream
当涉及持续不断地对数据进行读写时，流就出现了。

流可以分批处理文件内容：
```node
var stream = fs.createReadStream('my-file.txt');
stream.on('data', function(chunk){
	//deal with files
	})
```
能这样处理文件的原因是,流是循环运行的，不结束就一直存在。这种特性在日志记录时，也很有用。
打开文件的操作只要做一次。

以上是老的Stream。Stream 只是一个简单的继承自 EventEmitter 的类。就像上面的文件读取流，一旦开始读取，数据就会源源不断地读出，触发 'data' 事件——嗯一开始的确是一个不错的设计，但如果你想等一下，比如说等待某个回调后再开始处理这些数据。。。好吧，每一片数据只会冒出来一次，没人消费的话它就没了；OK 你想到了用 pause-stream「暂停」一下这个流，等下再消费？完了，数据会暂存在内存中，慢慢堆积，然后，你懂了。

### 现在的Stream
当然并不是说原来不断冒 'data' 的读取方式是不好的，只是说它对于某些情景，比如文件读取，比如网络传输，没办法控制读取速度而已。网络传输注定是没办法控制读取速度的，人家给你多少你都得吃进去；但文件读取，能不能按需要，处理多少读多少呢？

Node 0.10.X 就对 Stream 做了这样的改动：默认情况下，Node 会尽可能使用「拉」模式，也就是说只有 pipe 链末端的流消费者真正需要数据的时候，数据才会从源头被取出，然后顺着管子一路到达消费者。

##### 模拟 HTML POST 表单上传
```javascript
var request = require('request')
  , FormStream = require('formstream')

var form = FormStream()
  .field('title', 'ni dong de')
  .file('attachment', 'hexie.pkg')

var upload = request.post('http://xxxx.xxx/upload', {
  headers: form.headers()
}, function (err, res, body) {
  // ... 略
})

form.pipe(upload)
```
Stream 的精髓在于 .pipe() 嗯。


### 监视
fs.watchFile(path,callback()); //文件

fs.watch(path,callback());  //目录
