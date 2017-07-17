# Node.js-Notes
个人的 Node.js 学习笔记，有兴趣的小伙伴可以了解下，希望能帮到你们。
### 目录
1. [Introduction](#introduction)
	- [简介](#简介)
	- [特点](#特点)
	- [适合开发领域](#适合开发领域)
	- [总结](#总结)
2. [安装Node.js](#install-nodejs)
3. [NPM node.js 模块管理](#npm-nodejs-模块管理)	
	- [NPM简介](#npm简介)
	- [NPM常用命令](#npm常用命令)
4. [开始第一个 node.js web 实例](#开始第一个-nodejs-web-实例)
5. [node.js 回调函数](#nodejs-回调函数)
	- [1. 同步操作文件(阻塞I/O)](#1-同步操作文件阻塞io) 	
	- [2. 异步操作文件(非阻塞I/O)](#2-异步操作文件非阻塞io)
6. [Node.js 事件循环](#nodejs-事件循环)
	- [自定义模块](#自定义模块)
7. [Node.js 函数](#nodejs-函数)
	- [常用函数](#常用函数)
	- [匿名函数](#匿名函数)
8. [Node.js 路由](#nodejs-路由)
9. [Node.js 全局对象](#nodejs-全局对象)
	- [__filename](#__filename)
	- [__dirname](#__dirname)
	- [console](#console)
	- [process](#process)
10. [Node.js 常用工具](#nodejs-常用工具)
	- [util.inspect 将任意对象转换 为字符串](#utilinspect-将任意对象转换-为字符串)
	- [util.isArray() 判断是否是数组](#utilisarray-判断是否是数组)
	- [util.isBoolean() 判断是否是boolean类型的](#utilisboolean-判断是否是boolean类型的)
	- [util.isDate() 判断是否是日期](#utilisdate-判断是否是日期)
	- [util.isFunction() 判断是否是函数](#utilisfunction-判断是否是函数)
	- [util.isObject() 判断是否是对象](#utilisobject-判断是否是对象)
	- [util.isRegExp() 是否是正则对象](#utilisregexp-是否是正则对象)
11. [文件系统](#文件系统)
	- [读文件内容](#读文件内容)
		- [1. 异步非堵塞读取](#1-异步非堵塞读取)
		- [2. 同步堵塞读取](#2-同步堵塞读取)
	- [写文件内容](#写文件内容)
	- [删除文件](#删除文件)
	- [创建目录](#创建目录)
	- [删除目录](#删除目录)
12. [GET和POST请求](#get和post请求)
	- [GET请求](#get请求)
	- [POST请求](#post请求)
13. [工具模块](#工具模块)
	- [OS模块](#os模块)
	- [Path模块](#path模块)
	- [Net模块](#net模块)
	- [DNS模块](#dns模块)
	- [Domain模块](#domain模块)
14. [MySql](#mysql)
	- [查询数据](#查询数据)
	- [插入数据](#插入数据)
	- [更新数据](#更新数据)
	- [删除数据](#删除数据)
15. [HTTP模块](#http模块)
16. [静态文件夹](#静态文件夹)

# Introduction
## 简介 
> Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。<br> 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。<br> 
Node.js 的包管理器 npm，是全球最大的开源库生态系统。

## 特点
### 单线程
![单线程](http://i.imgur.com/aX2lBae.png)
> 在Java、PHP或者.net等服务器端语言中，会为每一个客户端连接创建一个新的线程。而每个线程需要耗费大约2MB内存。也就是说，理论上，一个8GB内存的服务器可以同时连接的最大用户数为4000个左右。要让Web应用程序支持更多的用户，就需要增加服务器的数量，而Web应用程序的硬件成本当然就上升了。<br>
Node.js不为每个客户连接创建一个新的线程，而仅仅使用一个线程。当有用户连接了，就触发一个内部事件，通过非阻塞I/O、事件驱动机制，让Node.js程序宏观上也是并行的。使用Node.js，一个8GB内存的服务器，可以同时处理超过4万用户的连接。<br>
另外，带线程的带来的好处，还有操作系统完全不再有线程创建、销毁的时间开销。
坏处，就是一个用户造成了线程的崩溃，整个服务都崩溃了，其他人也崩溃了。

### 非阻塞I/O non-blocking I/O 
> 例如，当在访问数据库取得数据的时候，需要一段时间。在传统的单线程处理机制中，在执行了访问数据库代码之后，整个线程都将暂停下来，等待数据库返回结果，才能执行后面的代码。也就是说，I/O阻塞了代码的执行，极大地降低了程序的执行效率。<br>
由于Node.js中采用了非阻塞型I/O机制，因此在执行了访问数据库的代码之后，将立即转而执行其后面的代码，把数据库返回结果的处理代码放在回调函数中，从而提高了程序的执行效率。<br>
当某个I/O执行完毕时，将以事件的形式通知执行I/O操作的线程，线程执行这个事件的回调函数。为了处理异步I/O，线程必须有事件循环，不断的检查有没有未处理的事件，依次予以处理。<br>
阻塞模式下，一个线程只能处理一项任务，要想提高吞吐量必须通过多线程。而非阻塞模式下，一个线程永远在执行计算操作，这个线程的CPU核心利用率永远是100%。所以，这是一种特别有哲理的解决方案：与其人多，但是好多人闲着；还不如一个人玩命，往死里干活儿。

### 事件驱动 event-driven
> 在Node中，客户端请求建立连接，提交数据等行为，会触发相应的事件。在Node中，在一个时刻，只能执行一个事件回调函数，但是在执行一个事件回调函数的中途，可以转而处理其他事件（比如，又有新用户连接了），然后返回继续执行原事件的回调函数，这种处理机制，称为“事件环”机制。<br>
Node.js底层是C++（V8也是C++写的）。底层代码中，近半数都用于事件队列、回调函数队列的构建。用事件驱动来完成服务器的任务调度，这是鬼才才能想到的。针尖上的舞蹈，用一个线程，担负起了处理非常多的任务的使命。

## 适合开发领域
> Node.js 善于I/O，不善于计算。因为Node.js最擅长的就是任务调度，如果你的业务有很多的CPU计算，实际上也相当于这个计算阻塞了这个单线程，就不适合Node开发。<br>
> 当应用程序需要处理大量并发的I/O，而在向客户端发出响应之前，应用程序内部并不需要进行非常复杂的处理的时候，Node.js非常适合。Node.js也非常适合与web socket配合，开发长连接的实时交互应用程序。<br>
> 比如:<br>
> - 用户表单收集
> - 考试系统
> - 聊天室
> - 图文直播
> - 提供JSON的API（为前台Angular使用）

## 总结
![原理图](http://i.imgur.com/DPHgbYn.png)
> 单线程，单线程的好处，减少了内存开销，操作系统的内存换页。如果某一个事情，进入了，但是被I/O阻塞了，所以这个线程就阻塞了。<br>
> 非阻塞I/O， 不会傻等I/O语句结束，而会执行后面的语句。<br>
> 事件机制，事件环，不管是新用户的请求，还是老用户的I/O完成，都将以事件方式加入事件环，等待调度。


# Install Node.js
1. 下载node.js
	
	前往[https://nodejs.org/en/](https://nodejs.org/en/ "https://nodejs.org/en/")下载node.js稳定版
	![下载稳定版](http://i.imgur.com/1byvubu.png)


2. 安装环境(windows 7 64位环境下)
	
	- 一直Next就可以了
	
	![安装](http://i.imgur.com/gRew5uL.png)
	
	- 在终端直接查看node.js的版本，查询到即安装成功
	
	![安装完成](http://i.imgur.com/awvnLdR.png)


3. 结合Sublime开发
	
	- 创建新的编译环境
	
	  ![创建新编译环境](http://i.imgur.com/IDga1Cv.png)
	- 在里面写入
	``` Bash
	{
		"cmd": ["node", "$file"],
		"selector": "source.js"
	}
	```
	- 保存并重命名
	
	![保存并重命名](http://i.imgur.com/Jzs3iNo.png)
	- 写一个js文件，输出Hello World.按Ctrl+B进行编译，如果无反应，请重启sublime，出现Hello World即为成功
	
	![sublime](http://i.imgur.com/LXs03iI.png)

# NPM node.js 模块管理 
## NPM简介
>NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：
>- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
>- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
>- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。
 
## NPM常用命令
1. `npm list`
	查看本地模块
2. `npm install mysql`
	安装mysql模块
3. `npm uninstall mysql`
	卸载mysql模块
4. `npm root`
	本地模块根目录
5. `npm root -g`
	本服务器所有模块根目录
6. `npm update mysql`
	升级mysql模块
7. `npm search mysql`
	搜索mysql模块
8. `npm -help <command>`
	查看帮助

# 开始第一个 node.js web 实例
```JavaScript
//加载http web模块
const http = require('http');
cs = function (req, res) {
	//设置head头
	res.writeHead('200',{'content-type':'text/html;charset=utf-8'});
	//给客户端返回内容
	res.write('hello world! Gavin first Node.js Web'); 
	res.end();
}
//监听端口
http.createServer(cs).listen(666);
console.log('http is ok');
```
![第一个web应用](http://i.imgur.com/uqEFm2H.png)
# node.js 回调函数
>Node.js 异步编程的直接体现就是回调。
异步编程依托于回调来实现，但不能说使用了回调后程序就异步化了。
回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。
例如，我们可以一边读取文件，一边执行其他命令，在文件读取完成后，我们将文件内容作为回调函数的参数返回。这样在执行代码时就没有阻塞或等待文件 I/O 操作。这就大大提高了 Node.js 的性能，可以处理大量的并发请求。

## 1. 同步操作文件(阻塞I/O)
```JavaScript
 //加载fs file 模块
 const fs = require('fs');
 file = "test.txt";
 //开始读取文件
 console.log('file start');
 //正在读取文件
 data = fs.readFileSync(file);
 console.log(data.toString());
 //读取文件结束
 console.log('file end！');
```
![同步操作文件](http://i.imgur.com/PuaItru.png)
## 2. 异步操作文件(非阻塞I/O)
```JavaScript
//加载fs file 模块
const fs = require('fs');
file = "test.txt";
//开始读取文件
console.log('file start');
//正在读取文件
//自带事件(当文件内容读取完毕时)
fs.readFile(file,function(err, data){
	 console.log(data.toString());
});
//读取文件结束
console.log('file end！');
```
![异步操作文件](http://i.imgur.com/kX5fFsu.png)
# Node.js 事件循环
>Node.js 是单进程单线程应用程序，但是通过事件和回调支持并发，所以性能非常高。<br>
>Node.js 的每一个 API 都是异步的，并作为一个独立线程运行，使用异步函数调用，并处理并发。<br>
>Node.js 基本上所有的事件机制都是用设计模式中观察者模式实现。<br>
>Node.js 单线程类似进入一个while(true)的事件循环，直到没有事件观察者退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数.<br>

```JavaScript
const events = require('events');
evt = new events.EventEmitter();
function eventHandler(){
	console.log('run');
}
//当触发eventName时，调用eventHandler
evt.on('eventName',eventHandler);
//触发eventName
evt.emit('eventName');
```

# Node.js 模块
> 为了让Node.js的文件可以相互调用，Node.js提供了一个简单的模块系统。<br>
模块是Node.js 应用程序的基本组成部分，文件和模块是一一对应的。换言之，一个 Node.js 文件就是一个模块，这个文件可能是JavaScript 代码、JSON 或者编译过的C/C++ 扩展。

## 自定义模块
在demo.js同级目录下创建show.js，编写模块代码
``` Javascript
// 自定义show模块
// 定义show类
function show() {
	this.name = 'user';
	this.say = function(){
		console.log('my name is ' + this.name);
	}
}
module.exports = new show();
```
在demo.js文件中调用我们自定义的show模块
``` JavaScript
//调用自定义模块
const show = require('./show');
show.say();
```

# Node.js 函数
## 常用函数
``` Javascript
function show(){
	console.log('show');
}
```
## 匿名函数
``` Javascript
show = function(){
	console.log('show');
}
```

# Node.js 路由
自己写个路由，一般实际应用大多是直接用框架
``` JavaScript
const http = require('http');
const url = require('url');
cs = function(request,res){
	urlStr = request.url; //获取访问url路径字符串
	urlPath = url.parse(urlStr).pathname; //转换url路径
	res.writeHead('200',{'content-type':'text/html;charset=utf-8'});
	switch (urlPath) { //选择页面
		case '/index':
			res.write('index page');
			break;
		case '/add':
			res.write('add page');
			break;
		case '/delete':
			res.write('delete page');
			break;
		case '/update':
			res.write('update page');
			break;
		case '/sreach':
			res.write('sreach');
			break;
		default:
			res.write('undefined page');
	}	
	console.log("访问的url路径:"+urlPath);
	res.end();
}
http.createServer(cs).listen(666);
console.log('Server Is Running Successfully!');
```
![路由](http://i.imgur.com/NAGU7hj.png)
# Node.js 全局对象
## __filename
本文件全路径<br>
`console.log(__filename);`<br>
输出：D:\NodeWorkSpace\demo.js
## __dirname
表示当前执行脚本所在的目录。<br>
`console.log(__dirname);`<br>
输出：D:\NodeWorkSpace
## console
1. `console.log` 向标准输出流打印字符并以换行符结束
2. `console.info` 该命令的作用是返回信息性消息，这个命令与console.log差别并不大，除了在chrome中只会输出文字外，其余的会显示一个蓝色的惊叹号。
3. `console.warn` 输出警告消息。控制台出现有黄色的惊叹号。
4. `console.error` 输出错误消息的。控制台在出现错误时会显示是红色的叉子。
5. `console.time`输出时间，表示计时开始。
6. `console.timeEnd`结束时间，表示计时结束

``` javascript
console.log("log");
console.info("information");
console.warn("warn");
console.error("error");
console.time('x');
for (var i = 0; i <= 100000; i++) {
	
}
console.timeEnd('x');
```
![console](http://i.imgur.com/pMfXeKB.png)
## process
### 属性
1. `stdout` 标准输出流。
2. `stderr` 标准错误流。
3. `stdin` 标准输入流。
4. `argv` argv 属性返回一个数组，由命令行执行脚本时的各个参数组成。它的第一个成员总是node，第二个成员是脚本文件名，其余成员是脚本文件的参数。
5. `execPath` 返回执行当前脚本的 Node 二进制文件的绝对路径。
6. `execArgv` 返回一个数组，成员是命令行下执行脚本时，在Node可执行文件与脚本文件之间的命令行参数。
7. `env` 返回一个对象，成员为当前 shell 的环境变量
8. `exitCode` 进程退出时的代码，如果进程优通过 process.exit() 退出，不需要指定退出码。
9. `version` Node 的版本，比如v0.10.18。
10. `versions` 一个属性，包含了 node 的版本和依赖.
11. `config` 一个包含用来编译当前 node 执行文件的 javascript 配置选项的对象。它与运行 ./configure 脚本生成的 "config.gypi" 文件相同。
12. `pid` 当前进程的进程号。
13. `title` 进程名，默认值为"node"，可以自定义该值。
14. `arch` 当前 CPU 的架构：'arm'、'ia32' 或者 'x64'。
15. `platform` 运行程序所在的平台系统 'darwin', 'freebsd', 'linux', 'sunos' 或 'win32'
16. `mainModule` require.main 的备选方法。不同点，如果主模块在运行时改变，require.main可能会继续返回老的模块。可以认为，这两者引用了同一个模块。

### 方法
1. `abort()` 这将导致 node 触发 abort 事件。会让 node 退出并生成一个核心文件。
2. `chdir(directory)` 改变当前工作进程的目录，如果操作失败抛出异常。
3. `cwd()` 返回当前进程的工作目录
4. `exit([code])` 使用指定的 code 结束进程。如果忽略，将会使用 code 0。
5. `getgid()` 获取进程的群组标识（参见 getgid(2)）。获取到得时群组的数字 id，而不是名字。
6. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
7. `setgid(id)` 设置进程的群组标识（参见 setgid(2)）。可以接收数字 ID 或者群组名。如果指定了群组名，会阻塞等待解析为数字 ID 。
8. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
9. `getuid()` 获取进程的用户标识(参见 getuid(2))。这是数字的用户 id，不是用户名。
10. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
11. `setuid(id)` 设置进程的用户标识（参见setuid(2)）。接收数字 ID或字符串名字。果指定了群组名，会阻塞等待解析为数字 ID 。
12. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
13. `getgroups()` 返回进程的群组 iD 数组。POSIX 系统没有保证一定有，但是 node.js 保证有。
14. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
15. `setgroups(groups)` 设置进程的群组 ID。这是授权操作，所有你需要有 root 权限，或者有 CAP_SETGID 能力。
16. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
17. `initgroups(user, extra_group)` 读取 /etc/group ，并初始化群组访问列表，使用成员所在的所有群组。这是授权操作，所有你需要有 root 权限，或者有 CAP_SETGID 能力。
18. 注意：这个函数仅在 POSIX 平台上可用(例如，非Windows 和 Android)。
19. `kill(pid[, signal])` 发送信号给进程. pid 是进程id，并且 signal 是发送的信号的字符串描述。信号名是字符串，比如 'SIGINT' 或 'SIGHUP'。如果忽略，信号会是 'SIGTERM'。
20. `memoryUsage()` 返回一个对象，描述了 Node 进程所用的内存状况，单位为字节。
21. `nextTick(callback)` 一旦当前事件循环结束，调用回到函数。
22. `umask([mask])` 设置或读取进程文件的掩码。子进程从父进程继承掩码。如果mask 参数有效，返回旧的掩码。否则，返回当前掩码。
23. `uptime()` 返回 Node 已经运行的秒数。
24. `hrtime()` 返回当前进程的高分辨时间，形式为 [seconds, nanoseconds]数组。它是相对于过去的任意事件。该值与日期无关，因此不受时钟漂移的影响。主要用途是可以通过精确的时间间隔，来衡量程序的性能。你可以将之前的结果传递给当前的 process.hrtime() ，会返回两者间的时间差，用来基准和测量时间间隔。

### 示例
``` JavaScript
version = process.version;
console.log(version);
cwd = process.cwd();
console.log(cwd);
```
输出：
v6.11.0
D:\NodeWorkSpace

# Node.js 常用工具
1. ### `util.inspect()` 将任意对象转换 为字符串
``` JavaScript
// 加载模块
const util = require('util');
obj = {'name':'Gavin','sex':'male','age':'16'}
console.log(typeof(obj));
console.log(util.inspect(obj));
console.log(typeof(util.inspect(obj)));
```
> 输出：
object
{ name: 'Gavin', sex: 'male', age: '16' }
string
2. ### `util.isArray()` 判断是否是数组
```JavaScript
// 加载模块
const util = require('util');
arr = ['a','b','c'];
console.log(util.isArray(arr));
```
>输出：true
3. ### `util.isBoolean()` 判断是否是boolean类型的
``` JavaScript
// 加载模块
const util = require('util');
obj = {'name':'Gavin','sex':'male','age':'16'}
console.log(util.isBoolean(obj));
```
>输出：false
4. ### `util.isDate()` 判断是否是日期
``` JavaScript
// 加载模块
const util = require('util');
tobj = new Date();
console.log(util.isDate(tobj))
```
>输出：true
5. ### `util.isFunction()` 判断是否是函数
6. ### `util.isObject()` 判断是否是对象
7. ### `util.isRegExp()` 是否是正则对象
``` JavaScript
// 加载模块
const util = require('util');
console.log(util.isRegExp(/^\d{11}$/ig));
```
>输出：true

# 文件系统
> 这里写个测试的文件`test.txt`内容如下
11111
22222
33333
44444
55555
66666

## 读文件内容
### 1. 异步非堵塞读取
> readFile();

```JavaScript
 //加载文件模块
const fs = require('fs');
file = 'test.txt';
//异步读取
fs.readFile(file,function(err,data){
	str = data.toString();
	console.log(str);
});
console.log('file read end！');
```
![异步](http://i.imgur.com/DmcVEga.png)
### 2. 同步堵塞读取
> readFileSync();

```JavaScript
//加载文件模块
const fs = require('fs');
file = 'test.txt';
//同步读取
data = fs.readFileSync(file);
str = data.toString();
console.log(str);
```
![同步读取](http://i.imgur.com/koKPCZT.png)

## 写文件内容
> writeFile();

``` JavaScript
const fs = require('fs');
file = 'test1.txt';
str  = "1111\n2222\n3333";
// 文件异步写入
fs.writeFile(file,str);
console.log('file write end！');
```
> 因为之前没有file1.txt所以就创建了一个新的文件夹

![](http://i.imgur.com/cEqt4Iu.png)<br>
![写入文件内容](http://i.imgur.com/lcoO6Ym.png)
## 删除文件
> unlink();

``` JavaScript
//加载文件模块
const fs = require('fs');
file = 'test1.txt';
fs.unlink(file);
```
## 创建目录
> mkdir();

``` JavaScript
//加载文件模块
const fs = require('fs');
file = 'testdir';
fs.mkdir(file);
```

## 删除目录
> rmdir();

``` JavaScript
//加载文件模块
const fs = require('fs');
file = 'testdir';
fs.rmdir(file);
```

# GET和POST请求
## GET请求
``` JavaScript
//加载web模块
const http = require('http');
//加载url模块
const url = require('url');
//加载querystring模块
const querystring = require('querystring');

cs = function(req, res){
	uri = req.url;
	if (uri !== '/favicon.ico') {
		console.log(uri);
		// 获取get参数字符串
		str = url.parse(uri).query;
		console.log(str);
		// 将参数字符串转换为json对象
		json = querystring.parse(str);
		console.log(json);
		res.write('Gavin\' Web server !');
		res.end();
	}
}
http.createServer(cs).listen(666);
console.log('Http Is Running Successfully!');
```
> 在浏览器输入url地址 http://localhost:666/index?id=1&name=Gavin

![](http://i.imgur.com/Pi0Ciw2.png)
![](http://i.imgur.com/47kM23w.png)

## POST请求
> POST请求代码

``` JavaScript
// 加载web模块
const http = require('http');
// 加载querystring模块
const querystring = require('querystring');
// 加载工具模块
const util = require('util');
// 服务器回调函数
cs = function(req, res){
	// 定义了一个post变量，用于暂存请求体的信息
    var post = '';     
    // 通过req的data事件监听函数，每当接受到请求体的数据，就累加到post变量中
    req.on('data', function(chunk){ 
    	console.log("chunk:"+chunk);   
        post += chunk;
    });
    // 在end事件触发后，通过querystring.parse将post解析为真正的POST请求格式，然后向客户端返回。
    req.on('end', function(){    
        post = querystring.parse(post);
        res.end(util.inspect(post));
    });
}
http.createServer(cs).listen(666);
console.log('Http Is Running Successfully!');
```
> POST请求测试页面

``` HTML
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Post请求测试页面</title>
</head>
<body>
	<form action="http://localhost:666/" method="post">
		<p>姓名：<input type="text" name="Name"></p>
		<p>年龄：<input type="text" name="Age"></p>
		<input type="submit">
	</form>
</body>
</html>
```
![](http://i.imgur.com/cajISzH.png)<br>
![](http://i.imgur.com/yiDU9tI.png)
![](http://i.imgur.com/T8vinq3.png)

# 工具模块
> 一些基本的系统操作函数

## OS模块
### 方法
#### `os.tmpdir()` 返回操作系统的默认临时文件夹。
#### `os.endianness()` 返回 CPU 的字节序，可能的是 "BE" 或 "LE"。
#### `os.hostname()` 返回操作系统的主机名。
#### `os.type()` 返回操作系统名
#### `os.platform()` 返回操作系统名
#### `os.arch()` 返回操作系统 CPU 架构，可能的值有 "x64"、"arm" 和 "ia32"。
#### `os.release()` 返回操作系统的发行版本。
#### `os.uptime()` 返回操作系统运行的时间，以秒为单位。
#### `os.loadavg()` 返回一个包含 1、5、15 分钟平均负载的数组。
#### `os.totalmem()` 返回系统内存总量，单位为字节。
#### `os.freemem()` 返回操作系统空闲内存量，单位是字节。
#### `os.cpus()` 返回一个对象数组，包含所安装的每个 CPU/内核的信息：型号、速度（单位 MHz）、时间（一个包含 user、nice、sys、idle 和 irq 所使用 CPU/内核毫秒数的对象）。
#### `os.networkInterfaces()` 获得网络接口列表。
### 属性
#### `os.EOL` 定义了操作系统的行尾符的常量。

### 实例
``` JavaScript
const os = require('os');
// 返回操作系统的默认临时文件夹。
console.log(os.tmpdir()); 
// 返回操作系统名
console.log(os.platform());
```
> 输出：
> C:\Users\ADMINI~1\AppData\Local\Temp
win32

## Path模块
### 方法
#### `path.normalize(p)` 规范化路径，注意'..' 和 '.'。
#### `path.join([path1][, path2][, ...])` 用于连接路径。该方法的主要用途在于，会正确使用当前系统的路径分隔符，Unix系统是"/"，Windows系统是"\"。
#### `path.resolve([from ...], to)` 将 to 参数解析为绝对路径。
#### `path.isAbsolute(path)` 判断参数 path 是否是绝对路径。
#### `path.relative(from, to)` 用于将相对路径转为绝对路径。
#### `path.dirname(p)` 返回路径中代表文件夹的部分，同 Unix 的dirname 命令类似。
#### `path.basename(p[, ext])` 返回路径中的最后一部分。同 Unix 命令 bashname 类似。
#### `path.extname(p)` 返回路径中文件的后缀名，即路径中最后一个'.'之后的部分。如果一个路径中并不包含'.'或该路径只包含一个'.' 且这个'.'为路径的第一个字符，则此命令返回空字符串。
#### `path.parse(pathString)` 返回路径字符串的对象。
#### `path.format(pathObject)` 从对象中返回路径字符串，和 path.parse 相反。

### 属性
#### `path.sep` 平台的文件路径分隔符，'\\' 或 '/'。
#### `path.delimiter` 平台的分隔符, ; or ':'.
#### `path.posix` 提供上述 path 的方法，不过总是以 posix 兼容的方式交互。
#### `path.win32` 提供上述 path 的方法，不过总是以 win32 兼容的方式交互

### 实例

``` JavaScript
const path = require('path');
// 判断是否是绝对路径
console.log(path.isAbsolute("C:/\Users/\ADMINI~1/\AppData/\Local/\Temp/\test.txt"));
//返回路径中文件的后缀名
console.log(path.extname("C:/\Users/\ADMINI~1/\AppData/\Local/\Temp/\test.txt"));
//平台的分隔符
console.log(path.delimiter);
```
> 输出
> true
.txt
;


## Net模块
### 方法
#### `net.createServer([options][, connectionListener])` 创建一个 TCP 服务器。参数 connectionListener 自动给 'connection' 事件创建监听器。
#### `net.connect(options[, connectionListener])` 返回一个新的 'net.Socket'，并连接到指定的地址和端口。
当 socket 建立的时候，将会触发 'connect' 事件。
#### `net.createConnection(options[, connectionListener])` 创建一个到端口 port 和 主机 host的 TCP 连接。 host 默认为 'localhost'。
#### `net.connect(port[, host][, connectListener])` 创建一个端口为 port 和主机为 host的 TCP 连接 。host 默认为 'localhost'。参数 connectListener 将会作为监听器添加到 'connect' 事件。返回 'net.Socket'。
#### `net.createConnection(port[, host][, connectListener])` 创建一个端口为 port 和主机为 host的 TCP 连接 。host 默认为 'localhost'。参数 connectListener 将会作为监听器添加到 'connect' 事件。返回 'net.Socket'。
#### `net.connect(path[, connectListener])` 创建连接到 path 的 unix socket 。参数 connectListener 将会作为监听器添加到 'connect' 事件上。返回 'net.Socket'。
#### `net.createConnection(path[, connectListener])` 创建连接到 path 的 unix socket 。参数 connectListener 将会作为监听器添加到 'connect' 事件。返回 'net.Socket'。
#### `net.isIP(input)` 检测输入的是否为 IP 地址。 IPV4 返回 4， IPV6 返回 6，其他情况返回 0。
#### `net.isIPv4(input)` 如果输入的地址为 IPV4， 返回 true，否则返回 false。
#### `net.isIPv6(input)` 如果输入的地址为 IPV6， 返回 true，否则返回 false。

### net.Server
> net.Server通常用于创建一个 TCP 或本地服务器

#### `server.listen(port[, host][, backlog][, callback])` 监听指定端口 port 和 主机 host ac连接。 默认情况下 host 接受任何 IPv4 地址(INADDR_ANY)的直接连接。端口 port 为 0 时，则会分配一个随机端口。
#### `server.listen(path[, callback])` 通过指定 path 的连接，启动一个本地 socket 服务器。
#### `server.listen(handle[, callback])` 通过指定句柄连接。
#### `server.listen(options[, callback])` options 的属性：端口 port, 主机 host, 和 backlog, 以及可选参数 callback 函数, 他们在一起调用server.listen(port, [host], [backlog], [callback])。还有，参数 path 可以用来指定 UNIX socket。
#### `server.close([callback])` 服务器停止接收新的连接，保持现有连接。这是异步函数，当所有连接结束的时候服务器会关闭，并会触发 'close' 事件。
#### `server.address()` 操作系统返回绑定的地址，协议族名和服务器端口。
#### `server.unref()` 如果这是事件系统中唯一一个活动的服务器，调用 unref 将允许程序退出。
#### `server.ref()` 与 unref 相反，如果这是唯一的服务器，在之前被 unref 了的服务器上调用 ref 将不会让程序退出（默认行为）。如果服务器已经被 ref，则再次调用 ref 并不会产生影响。
#### `server.getConnections(callback)` 异步获取服务器当前活跃连接的数量。当 socket 发送给子进程后才有效；回调函数有 2 个参数 err 和 count。

### 事件
#### `listening` 当服务器调用 server.listen 绑定后会触发。
#### `connection` 当新连接创建后会被触发。socket 是 net.Socket实例。
#### `close` 服务器关闭时会触发。注意，如果存在连接，这个事件不会被触发直到所有的连接关闭。
#### `error` 发生错误时触发。'close' 事件将被下列事件直接调用。

### net.Socket
>net.Socket 对象是 TCP 或 UNIX Socket 的抽象。net.Socket 实例实现了一个双工流接口。 他们可以在用户创建客户端(使用 connect())时使用, 或者由 Node 创建它们，并通过 connection 服务器事件传递给用户。

### net.Socket事件
#### `lookup` 在解析域名后，但在连接前，触发这个事件。对 UNIX sokcet 不适用。
#### `connect` 成功建立 socket 连接时触发。
#### `data` 当接收到数据时触发。
#### `end` 当 socket 另一端发送 FIN 包时，触发该事件。
#### `timeout` 当 socket 空闲超时时触发，仅是表明 socket 已经空闲。用户必须手动关闭连接。
#### `drain` 当写缓存为空得时候触发。可用来控制上传。
#### `error` 错误发生时触发。
#### `close` 当 socket 完全关闭时触发。参数 had_error 是布尔值，它表示是否因为传输错误导致 socket 关闭。

### net.Socket属性
#### `socket.bufferSize` 该属性显示了要写入缓冲区的字节数。
#### `socket.remoteAddress` 远程的 IP 地址字符串，例如：'74.125.127.100' or '2001:4860:a005::68'。
#### `socket.remoteFamily` 远程IP协议族字符串，比如 'IPv4' or 'IPv6'。
#### `socket.remotePort` 远程端口，数字表示，例如：80 or 21。
#### `socket.localAddress` 网络连接绑定的本地接口 远程客户端正在连接的本地 IP 地址，字符串表示。例如，如果你在监听'0.0.0.0'而客户端连接在'192.168.1.1'，这个值就会是 '192.168.1.1'。
#### `socket.localPort` 本地端口地址，数字表示。例如：80 or 21。
#### `socket.bytesRead` 接收到得字节数。
#### `socket.bytesWritten` 发送的字节数。

### net.Socket方法
#### `new net.Socket([options])` 构造一个新的 socket 对象。
#### `socket.connect(port[, host][, connectListener])` 指定端口 port 和 主机 host，创建 socket 连接 。参数 host 默认为 localhost。通常情况不需要使用 net.createConnection 打开 socket。只有你实现了自己的 socket 时才会用到。
#### `socket.connect(path[, connectListener])` 打开指定路径的 unix socket。通常情况不需要使用 net.createConnection 打开 socket。只有你实现了自己的 socket 时才会用到。
#### `socket.setEncoding([encoding])` 设置编码
#### `socket.write(data[, encoding][, callback])` 在 socket 上发送数据。第二个参数指定了字符串的编码，默认是 UTF8 编码。
#### `socket.end([data][, encoding])` 半关闭 socket。例如，它发送一个 FIN 包。可能服务器仍在发送数据。
#### `socket.destroy()` 确保没有 I/O 活动在这个套接字上。只有在错误发生情况下才需要。（处理错误等等）。
#### `socket.pause()` 暂停读取数据。就是说，不会再触发 data 事件。对于控制上传非常有用。
#### `socket.resume()` 调用 pause() 后想恢复读取数据。
#### `socket.setTimeout(timeout[, callback])` socket 闲置时间超过 timeout 毫秒后 ，将 socket 设置为超时。
#### `socket.setNoDelay([noDelay])` 禁用纳格（Nagle）算法。默认情况下 TCP 连接使用纳格算法，在发送前他们会缓冲数据。将 noDelay 设置为 true 将会在调用 socket.write() 时立即发送数据。noDelay 默认值为 true。
#### `socket.setKeepAlive([enable][, initialDelay])` 禁用/启用长连接功能，并在发送第一个在闲置 socket 上的长连接 probe 之前，可选地设定初始延时。默认为 false。 设定 initialDelay （毫秒），来设定收到的最后一个数据包和第一个长连接probe之间的延时。将 initialDelay 设为0，将会保留默认（或者之前）的值。默认值为0.
#### `socket.address()` 操作系统返回绑定的地址，协议族名和服务器端口。返回的对象有 3 个属性，比如{ port: 12346, family: 'IPv4', address: '127.0.0.1' }。
#### `socket.unref()` 如果这是事件系统中唯一一个活动的服务器，调用 unref 将允许程序退出。如果服务器已被 unref，则再次调用 unref 并不会产生影响。
#### `socket.ref()` 与 unref 相反，如果这是唯一的服务器，在之前被 unref 了的服务器上调用 ref 将不会让程序退出（默认行为）。如果服务器已经被 ref，则再次调用 ref 并不会产生影响。

### 实例
> Telnet 服务器实例

``` JavaScript
const net = require('net');
chat = net.createServer();
//但客户端连接时
chat.on('connection', function(client){
	// 给客户端发送Hello World
	client.write('hello world');
	// 当接收到发来信息时
	client.on('data',function(data){
		console.log(data.toString());
	});
});
chat.listen(9000);
console.log('server ok');
```
> 在Windows Dos界面(cmd)，输入: telnet 127.0.0.1 9000

![Telnet服务器实例](http://i.imgur.com/MD3nyZv.png)

## DNS模块
### 方法
#### `dns.lookup(hostname[, options], callback)` 将域名（比如 'runoob.com'）解析为第一条找到的记录 A （IPV4）或 AAAA(IPV6)。参数 options可以是一个对象或整数。如果没有提供 options，IP v4 和 v6 地址都可以。如果 options 是整数，则必须是 4 或 6。
#### `dns.lookupService(address, port, callback)` 使用 getnameinfo 解析传入的地址和端口为域名和服务。
#### `dns.resolve(hostname[, rrtype], callback)` 将一个域名（如 'runoob.com'）解析为一个 rrtype 指定记录类型的数组。
#### `dns.resolve4(hostname, callback)` 和 dns.resolve() 类似, 仅能查询 IPv4 (A 记录）。 addresses IPv4 地址数组 (比如，['74.125.79.104', '74.125.79.105', '74.125.79.106']）。
#### `dns.resolve6(hostname, callback)` 和 dns.resolve4() 类似， 仅能查询 IPv6( AAAA 查询）
#### `dns.resolveMx(hostname, callback)` 和 dns.resolve() 类似, 仅能查询邮件交换(MX 记录)。
#### `dns.resolveTxt(hostname, callback)` 和 dns.resolve() 类似, 仅能进行文本查询 (TXT 记录）。 addresses 是 2-d 文本记录数组。(比如，[ ['v=spf1 ip4:0.0.0.0 ', '~all' ] ]）。 每个子数组包含一条记录的 TXT 块。根据使用情况可以连接在一起，也可单独使用。
#### `dns.resolveSrv(hostname, callback)` 和 dns.resolve() 类似, 仅能进行服务记录查询 (SRV 记录）。 addresses 是 hostname可用的 SRV 记录数组。 SRV 记录属性有优先级（priority），权重（weight）, 端口（port）, 和名字（name） (比如，[{'priority': 10, 'weight': 5, 'port': 21223, 'name': 'service.example.com'}, ...]）。
#### `dns.resolveSoa(hostname, callback)` 和 dns.resolve() 类似, 仅能查询权威记录(SOA 记录）。
#### `dns.resolveNs(hostname, callback)` 和 dns.resolve() 类似, 仅能进行域名服务器记录查询(NS 记录）。 addresses 是域名服务器记录数组（hostname 可以使用） (比如, ['ns1.example.com', 'ns2.example.com']）。
#### `dns.resolveCname(hostname, callback)` 和 dns.resolve() 类似, 仅能进行别名记录查询 (CNAME记录)。addresses 是对 hostname 可用的别名记录数组 (比如，, ['bar.example.com']）。
#### `dns.reverse(ip, callback)` 反向解析 IP 地址，指向该 IP 地址的域名数组。
#### `dns.getServers()` 返回一个用于当前解析的 IP 地址数组的字符串。
#### `dns.setServers(servers)` 指定一组 IP 地址作为解析服务器。

### 实例
``` JavaScript
const dns = require('dns');
domain = "www.baidu.com";
// 通过域名找IP
dns.lookup(domain,function(err,ip,family){
	console.log('IP is ：' + ip);
});
```
> 输出： IP is ：14.215.177.38

## Domain模块
### 方法
#### `domain.run(function)` 在域的上下文运行提供的函数，隐式的绑定了所有的事件分发器，计时器和底层请求。
#### `domain.add(emitter)` 显式的增加事件
#### `domain.remove(emitter)` 删除事件。
#### `domain.bind(callback)` 返回的函数是一个对于所提供的回调函数的包装函数。当调用这个返回的函数被时，所有被抛出的错误都会被导向到这个域的 error 事件。
#### `domain.intercept(callback)` 和 domain.bind(callback) 类似。除了捕捉被抛出的错误外，它还会拦截 Error 对象作为参数传递到这个函数。
#### `domain.enter()` 进入一个异步调用的上下文，绑定到domain。
#### `domain.exit()` 退出当前的domain，切换到不同的链的异步调用的上下文中。对应domain.enter()。
#### `domain.dispose()` 释放一个domain对象，让node进程回收这部分资源。
#### `domain.create()` 返回一个domain对象。

### 属性
#### `domain.members` 已加入domain对象的域定时器和事件发射器的数组。


# MySql
> 首先安装mysql模块
> 通过命令：npm install mysql
> 创建数据库node_mysql
> 创建user表,表字典如下

|字段名|属性|备注|
|:--:|:--:|:--:|
|id|int|主键|
|name|varchar|非空|
|age|int|非空|

> 数据库插入数据后的表

![](http://i.imgur.com/zfZFrxW.png)

## 查询数据
``` JavaScript
//查询数据
const mysql = require('mysql');
conn = mysql.createConnection({
	host:'localhost',
	user:'root',
	password:'abcdef123',
	port:'3306',
	database:'node_mysql',
});
//建立连接
conn.connect();
//准备sql语句
sql = "select * from user";
//查询数据库
conn.query(sql,function(err,result){
	if (err) {
		console.log('error:',err.message);
	}else {
		for(i = 0; i < result.length; i++){
			console.log("id  :" + result[i]['id']);
			console.log("name:" + result[i]['name']);
			console.log("age :" + result[i]['age']);
			console.log("-----------------");
		}
	}
});
//关闭数据库连接
conn.end();
```
> 输出结果：<br>
> id  :1<br>
name:Gavin<br>
age :16<br>
\-----------------<br>
id  :2<br>
name:Gavin<br>
age :18<br>
\-----------------

## 插入数据
``` JavaScript
//插入数据
const mysql = require('mysql');
conn = mysql.createConnection({
	host:'localhost', 
	user:'root',
	password:'abcdef123',
	port:'3306',
	database:'node_mysql',
});
//建立连接
conn.connect();
//准备sql语句
sql = "insert into user(name,age) values(?,?)";
data = ['Gavin','22'];
//查询数据库
conn.query(sql,data,function(err,result){
	if (err) {
		console.log('error:',err.message);
	}else {
		console.log('AffectedRows :',result['affectedRows']);
	}
});
//关闭数据库连接
conn.end();
```
> 输出：AffectedRows : 1 

![](http://i.imgur.com/Bs7FpEK.png)

## 更新数据
``` JavaScript
//更新数据
const mysql = require('mysql');
conn = mysql.createConnection({
	host:'localhost', 
	user:'root',
	password:'abcdef123',
	port:'3306',
	database:'node_mysql',
});
//建立连接
conn.connect();
//准备sql语句
sql = "update user set age=? where id=?";
data = ['15','1'];
//查询数据库
conn.query(sql,data,function(err,result){
	if (err) {
		console.log('error:',err.message);
	}else {
		console.log('AffectedRows :',result['affectedRows']);
	}
});
//关闭数据库连接
conn.end();
```
> 输出：AffectedRows : 1

![](http://i.imgur.com/CnZsCyo.png)

## 删除数据
``` JavaScript
//删除数据
const mysql = require('mysql');
conn = mysql.createConnection({
	host:'localhost', 
	user:'root',
	password:'abcdef123',
	port:'3306',
	database:'node_mysql',
});
//建立连接
conn.connect();
//准备sql语句
sql = "delete from user where id=1";
//查询数据库
conn.query(sql,function(err,result){
	if (err) {
		console.log('error:',err.message);
	}else {
		console.log('AffectedRows :',result['affectedRows']);
	}
});
//关闭数据库连接
conn.end();
```
> 输出：AffectedRows : 1

![删除数据](http://i.imgur.com/44bkwD6.png)

# HTTP模块
``` JavaScript
//引用模块
var http = require("http");

//创建一个服务器，回调函数表示接收到请求之后做的事情
var server = http.createServer(function(req,res){
	//req参数表示请求，res表示响应
	console.log("服务器接收到了请求" + req.url);
	//设置头部
	res.writeHead(200,{"Content-Type":"text/html;charset=UTF8"});
	res.write("<h1>我是主标题</h1>");
	res.write("<h2>我是2标题</h2>");
	res.write("<h3>我是3标题</h3>");
	res.end((1+2+3).toString());
});

//监听端口
server.listen(3000,"127.0.0.1");
```
## Request
### url 模块
``` JavaScript
var http = require("http");

var server = http.createServer(function(req,res){
	console.log(req.url);
	res.end();
});

server.listen(3000,"127.0.0.1");
```
``` JavaScript
var http = require("http");
var url = require("url");

var server = http.createServer(function(req,res){
	//url.parse()可以将一个完整的URL地址，分为很多部分：
	//host、port、pathname、path、query
	var pathname = url.parse(req.url).pathname;
	//url.parse()如果第二个参数是true，那么就可以将所有的查询变为对象
	//就可以直接打点得到这个参数
	var query = url.parse(req.url,true).query;
	//直接打点得到这个参数
	var age = query.age;
	
	console.log("pathname:" + pathname);
	console.log(query);
	console.log("age:" + age);
	
	res.end();
});

server.listen(3000,"127.0.0.1");
```
> 输入网址：http://localhost:3000/?age=18<br>
> 输出：<br>
> pathname:/<br>
> { age: '18' } <br>
> age:18 <br>

# 静态文件夹
> 访问静态文件夹<br>
> 文件目录<br>
>  - static
>    - index.html
>    - 404.html
>  - app.js

--------------------- 

> app.js

```JavaScript
var http = require("http");
var url = require("url");
var fs = require("fs"); 
var path = require("path");

var server = http.createServer(function(req,res){
    // 获取用户的路径
    var pathname = url.parse(req.url).pathname;
    // 获取拓展名
	var extname = path.extname(pathname);
    // 读取这个文件
    fs.readFile("./static/" + pathname, function(err, data){
        if(err){
            // 如果此文件不存在， 就返回404返回
            fs.readFile("./static/404.html", function(err, data){
                res.writeHead(404, {"Content-type": "text/html;charset = UTF8"});
                res.end(data);
            });
            return;
        };
        var mime = getMime(extname);
		res.writeHead(200,{"Content-type":mime});
        res.end(data);
    })
});
function getMime(extname){
    switch(extname){
        case ".html":
            return "text/html";
            break;
        case ".jpg":
            return "image/jpg";
            break;
        case ".css":
            return "text/css";
            break;
        case ".js":
            return "text/javascript";
            break;
    }
}
server.listen(3000,"127.0.0.1");
```
> static/index.html

``` HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>这是一个静态的HTML页面</h1>
</body>
</html>
```
> static/404.html

``` HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>这是一个404页面</h1>
</body>
</html>
```