---
layout: post
title:  "开发Chrome扩展——Nodejs Debugger"
date:   2018-10-05 20:20:29 +0800
categories: Node
---

![Node.js Debugger](http://upload-images.jianshu.io/upload_images/5796375-be1c852b0ca34f7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 目录
1. Node.js Debugger扩展展示及下载地址
2. Node.js Debugger使用介绍
3. Node.js Debugger开发思想
4. 待改进
5. 相关资料

### 1. Node.js Debugger扩展展示及下载地址
#### Node.js Debugger扩展展示
1. 初始页面
![初始展示页面](http://upload-images.jianshu.io/upload_images/5796375-6a69581ae22d879d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 操作失败页面
![操作失败页面](http://upload-images.jianshu.io/upload_images/5796375-d4b5f305466e8e31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Node.js Debugger扩展下载地址

**百度网盘地址：**[https://pan.baidu.com/s/1c1KLk5a](https://pan.baidu.com/s/1c1KLk5a)

### 2. Node.js Debugger使用介绍
#### 安装Chrome扩展
1. 打开Chrome扩展程序面板。![扩展工具程序面板入口](http://upload-images.jianshu.io/upload_images/5796375-508c712155506f9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.直接将的.crx文件拖拽到扩展程序面板中，添加扩展程序。![屏幕快照 2017-11-30 13.23.31.png](http://upload-images.jianshu.io/upload_images/5796375-caee8bf6ad115fd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 添加扩展程序成功，可以在工具栏看到我们扩展程序的图标（绿色小图标）。![添加成功提示](http://upload-images.jianshu.io/upload_images/5796375-b7228f890c2f0283.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Node.js Debugger扩展程序使用介绍
1. 输入主机名和端口号，默认情况下主机名为localhost，端口号为9229，如果在启动node inspector时，你使用的不是--inspect，而是--inspect=127.0.0.1:8000，此时Chrome与node inspector的通信端口为8000，因此，你需要重新设置端口。
**情况一：**
![使用--inspect参数](http://upload-images.jianshu.io/upload_images/5796375-fe7160ba73ad2033.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)此时，打开Chrome，点击扩展程序，直接点击调试即可。

**情况二：**
![使用--inspect=ip:port参数](http://upload-images.jianshu.io/upload_images/5796375-398cd9ec38464978.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![设置端口号9000](http://upload-images.jianshu.io/upload_images/5796375-d3b636b04125b15c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，就必须设置端口号，如果不设置点击调试的话，就会弹出弹出层，而不能正确访问调试页面。

#### 使用过程中注意事项
1. 确保node版本是7.x.x以上
node inspector功能是在7.x.x版本以上才能使用。因此，如果当你点击调试按钮，弹出先标签页，但是页面一直在转圈，此时，你就要查看一下，是否是你的node版本太低了。

2. 确保启动了node inspector，并且**建议使用--inspect-brk来启动**
如果你使用--inspect启动node inspector，并且你调试的只是一个过程式的js，此时你的代码在调试之前就已经执行完，无法再进行调试，而是用--inspect-brk来启动，将在所有程序的开头开始调试，此时，你便可以在调试面板上添加断点进行调试。

3. 端口号设置问题
确定输入框中设置的端口号是否正确，并且端口号是否已经被占用。如果你设置的端口号已经已经被使用了，此时插件内部可能报错，但是并不会直接显示到插件展示页面上，因此，如果发现点击调试按钮不弹出弹出层，也不弹出新的标签页，请检查你的端口号是否被占用了。

4. 一次调试完成后，不能直接刷新页面，开始下一轮调试
此时需要重新启动新的node inspector，在按照上面的步骤打开页面，否则可能会出现，无法进行调试，或者无法打开调试页面的情况。

### 3. Node.js Debugger开发思想
Inspector clients must know and specify host address, port, and UUID to connect to the WebSocket interface. The full URL is ws://127.0.0.1:9229/0f2c936f-b1cd-4ac9-aab3-f63b0f33d55e, of course dependent on actual host and port and with the correct UUID for the instance.
Node.js v7+版本提供了一个inspector的功能，在使用node运行js程序时，可以添加--inspector打开这个功能。
**该功能为Chrome DevTools提供了一个通信接口，这是的Node进程可以和Chrome DevTools使用websocket直接进行通信**。
同时，也提供了用户接口，用户可以通过访问
**http://IP:port/json/list**访问到这个接口的相关信息，其中webSocketDebuggerUrl就是Chrome DevTools和Node进程的websocket通信地址，而**devtoolsFrontendUrl，就是我们可以访问的调试面板地址**。
```
[ {
  "description": "node.js instance",
  "devtoolsFrontendUrl": "chrome-devtools://devtools/bundled/inspector.html?experiments=true&v8only=true&ws=127.0.0.1:9000/ce3d915c-367e-4751-980c-a50cede6379d",
  "faviconUrl": "https://nodejs.org/static/favicon.ico",
  "id": "ce3d915c-367e-4751-980c-a50cede6379d",
  "title": "test.js",
  "type": "node",
  "url": "file:///Users/yyp/Desktop/test.js",
  "webSocketDebuggerUrl": "ws://127.0.0.1:9000/ce3d915c-367e-4751-980c-a50cede6379d"
} ]
```
因此我们**通过http://IP:port/json/list返回数据中的devtoolsFrontendUrl字段，获取调试页面地址，然后在chrome插件中创建一个新的标签页，并且将该字段作为新标签页的url即可**。

### 4. 待改进
1. 启动扩展程序时，第一个输入框被选中，出现蓝色背景。
2. 未提供用户设置选项。

### 5. 相关资料
1. node inspector 相关
[**Debugging Guide**: https://nodejs.org/en/docs/guides/debugging-getting-started/#debugging-guide](https://nodejs.org/en/docs/guides/debugging-getting-started/#debugging-guide)
[**Debugging Node.js Apps**: https://nodejs.org/en/docs/inspector/](https://nodejs.org/en/docs/inspector/)
[**如何在Chrome DevTools 中对Node程序进行调试**: http://www.jianshu.com/p/3ed910d3866c](http://www.jianshu.com/p/3ed910d3866c)

2. Chrome 插件开发相关
[**Chrome扩展及应用开发（首发版）**:http://www.ituring.com.cn/book/1421](http://www.ituring.com.cn/book/1421)
[**百度浏览器开发文档**：https://chajian.baidu.com/developer/extensions/getstarted.html](https://chajian.baidu.com/developer/extensions/getstarted.html)

3. 源码
[**github地址**：https://github.com/DiligentYe/todo-list-chrome-extension/tree/nodejs-debugger](https://github.com/DiligentYe/todo-list-chrome-extension/tree/nodejs-debugger)

















