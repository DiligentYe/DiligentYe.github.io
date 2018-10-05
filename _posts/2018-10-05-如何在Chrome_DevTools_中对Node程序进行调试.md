---
layout: post
title:  "如何在Chrome DevTools中对Node程序进行调试"
date:   2018-10-05 20:20:29 +0800
categories: Javascript
---

![Node](http://upload-images.jianshu.io/upload_images/5796375-1c572fef179283b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相信有很多人和我一样，习惯了使用chrome调试js程序，然而node刚开始提供的调试方式只用Debugger，只能通过node --debug xxx.js启动命令行调试工具，及其的不方便。当然也有一些插件在此基础上，使用websocket进行通信，使其可以在chrome浏览器中调试。体验和直接在chrome上进行调试还是差了很多。

然而在Node v7.x.x后，Node有提供了一个Inspector，可以直接和Chrome DevTools进行通信，下面来详细介绍进入调试的步骤，以及在使用过程中，我遇到的问题及解决办法。

### 目录
1. 具体调试步骤详细介绍
2. 问题及解决方法
3. 其他工具
4. 相关文档

### 具体调试步骤详细介绍
#### 1. Chrome DevTools和Node版本要求
1. **Chrome DevTools：** 55+ 
在地址栏中输入__chrome://settings/help__，查看Chrome版本
![Chroem版本](http://upload-images.jianshu.io/upload_images/5796375-4d4854d3f2763edb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. **Node.js：** v7.x.x+
在命令行中输入__node --version__进行查看
![Node.js版本](http://upload-images.jianshu.io/upload_images/5796375-7d85021a1cbbee6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2. 运行脚本，并访问调试页面
**总体步骤：**先根据具体情况，选用不同的参数运行脚本；然后访问相应的url获取调试页面的访问地址；最后访问这个地址，进入调试页面。

在命令行中运行相应脚本，使用__--inspect__，或者__--inspect-brk__开启调试开关，如__node --inspect path/xxx.js__ 或者__node --inspect-brk path/xxx.js__。下面根据不同情况进行具体分析。

##### 情况1
首先启动脚本，如果你的脚本搭建http或者net服务器，你可以直接使用--inspect。如
```
let net = require('net');

// 创建一个net服务器
const server = net.createServer();

// 连接回调函数
server.on('connection', (conn) => {
	console.log('connection');
});

// 监听8080端口
server.listen(8080);

// 监听回调函数
server.on('listening', () => {
	console.log('listening')
});
```
![使用--inspect后显示结果](http://upload-images.jianshu.io/upload_images/5796375-990fe98c73d601de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注：**
1. 上图是在v8.9.1版本时显示的结果，后面会一次列举出其他版本下的结果。
2. Debugger listening on **ws://127.0.0.1:9229/890b7b49-c744-4103-b0cd-6c5e8036be95**，其中给定的url并不是提供给我们在Chrome浏览器中访问的地址，而是Node.js和Chrome之间进行通信的的地址，它们通过websocket通过指定的端口进行通信，从而将调试结果实时展示在Chrome浏览器中。

 然后，访问__http://IP:port/json/list__（其中IP就是主机的IP地址，通常为127.0.0.1，port则是端口号，默认为9229，这是Node提供给任意调试工具链接调试的协议，可以通过命令行参数进行修改[参数文档地址](https://nodejs.org/en/docs/guides/debugging-getting-started/#command-line-options)），会返回相应http请求的元数据，包括WebSocket URL，UUID，Chrome DevTools URL。其中，__WebSocket URL__就是Node.js和Chrome之间的通信地址；__UUID__是一个特定的标识，每一个进程都会分配一个uuid，因此每一次调用会有出现不同的结果；__Chrome DevTools URL__就是我们的调试页面的url。

如访问http://127.0.0.1:9229/json/list，我们将会得到一下结果，其中**devtoolsFrontendUrl**就是我们需要访问的地址。
![屏幕快照 2017-11-22 14.34.13.png](http://upload-images.jianshu.io/upload_images/5796375-e77a4bb46f00b3c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后，访问这个地址后显示页面：
![调试页面](http://upload-images.jianshu.io/upload_images/5796375-0bcf4330cbeecd74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 情况2（重要）
如果你的脚本运行完之后直接结束进程，那么你需要使用**--inspect-brk**来启动调试器，这样使得脚本可以代码执行之前break，否则，整个代码直接运行到代码结尾，结束进程，根本无法进行调试。如：
```
function sayHi(name) {
	console.log('Hello, ' + name);
}

sayHi('yyp');
```
1. 使用**--inspect**:
![直接使用--inspect](http://upload-images.jianshu.io/upload_images/5796375-43731f40bc3f85d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

直接使用--inspect，代码执行完毕后，进程直接结束，因此没有办法在进行调试。

2. 使用**--inspect-brk**:
![使用--inspect-brk](http://upload-images.jianshu.io/upload_images/5796375-dd9624de814d050c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![访问127.0.0.1:9229](http://upload-images.jianshu.io/upload_images/5796375-3ac7df02a17adea4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![调试页面](http://upload-images.jianshu.io/upload_images/5796375-44e4c817051f5107.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面三张图中可以看出，此时该Node进程并没有直接退出，并且我们可以通过访问http://127.0.0.1:9229/json/list获取页面url，并且在调试页面中我们也发现，程序在第一行break。

细心的你，可能还发现，与我们所写的程序不同的是，调试页面中，将我们的程序包裹在一个函数中，因为浏览器中不存在相应的对象（实际原因可以自行去研究）。

### 其他工具
**node-inspector**
在使用这个工具之前，我一直在使用node-inspector，只需要使用npm install -g node-inspector安装node-inspector命令行工具，然后在使用node-inspector path/xxx.js即可启动调试。
**不足之处：**调试体验比较差，相对Chrome DevTools使用，感觉不够流畅，并且在较高版本中，debugger被废弃（v7.7.0+），进而无法使用，可能会报错（v8.9.1版本直接报错）。

**vsCode + webStorm**
提供直接调试功能（未使用过）。

### 问题及解决方法
#### 1. 网上传统的过时设置方法[访问地址](https://hospodarets.com/nodejs-debugging-in-chrome-devtools?utm_source=javascriptweekly&utm_medium=email)
![过时设置方法](http://upload-images.jianshu.io/upload_images/5796375-4f19a57762518c1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

出现的问题，就是按照以上步骤操作之后，我们并没有发现Node debugging的选项，此时，就不知道如何进行下去了。在这里给出的解释是，**在最新版的Chrome浏览器中，Node debugging不需要手动去启动了，而是默认就是可以使用的**。

#### 1. 不同Node版本出现的问题
分别在三个大版本下进行了测试（v6.9.2，v7.3.0，v8.9.1）

##### v6.9.2 版本下使用情况
![命令行执行结果](http://upload-images.jianshu.io/upload_images/5796375-d2624c9ea63dc606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![访问http://127.0.0.1:9229/json/list](http://upload-images.jianshu.io/upload_images/5796375-b78f54fcbedafc35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时你会发现，命令行已经给你访问Chrome的url，并且这个url和访问http://127.0.0.1:9229/json/list得到的devtoolsFrontendUrl属性值一样，但是，**当你访问这个url时，浏览器却不能显示调试页面，此时，说明你的Node版本太低，需要使用更高版本。**

##### v7.3.0 版本下使用情况
![命令行执行结果](http://upload-images.jianshu.io/upload_images/5796375-43f50340d96e57da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![访问http://127.0.0.1:9229/json/list](http://upload-images.jianshu.io/upload_images/5796375-18f3e2946c136789.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![调试页面](http://upload-images.jianshu.io/upload_images/5796375-c60a41dc5325e072.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，命令行也提供了访问调试页面的链接。

##### v8.9.1 版本下使用情况
![命令行执行结果](http://upload-images.jianshu.io/upload_images/5796375-b9cc23530c5678ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![访问http://127.0.0.1:9229/json/list](http://upload-images.jianshu.io/upload_images/5796375-3d657325609d22db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![调试页面](http://upload-images.jianshu.io/upload_images/5796375-449e9b12b94ff796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，命令行给定的的url并不是调试页面的url，因此必须通过访问http://127.0.0.1:9229/json/list来获取调试页面url。

### 相关文档
[Debugging Guide](https://nodejs.org/en/docs/guides/debugging-getting-started/#command-line-options)
[Debugging Node.js Apps](https://nodejs.org/en/docs/inspector/)








