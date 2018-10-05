---
layout: post
title:  "MAC终端中如何在命令行中启动Sublime编辑器"
date:   2018-10-05 20:20:29 +0800
categories: Tool
---

### 目标
在终端输入__> sublime__，可以直接打开sublime应用程序；在终端输入__> sublime 文件或者目录__，打开sublime应用并打开对应的文件或目录。

### 实现方法
1. 创建快捷方式
使用软连接（可以理解为快捷方式）将Sublime提供的命令行工具直接连接到/usr/local/bin/这个路径下。将/usr/local/bin/subl连接到Mac下的sublime应用提供的命令行工具subl。
```
ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl
```

![执行结果](http://upload-images.jianshu.io/upload_images/5796375-2fe59d0e69af81b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 修改命令名
完成上面步骤，我们便可以直接通过subl命令打开sublime应用程序了，如果你想通过sublime这个命令打开的话，需要通过别名进行设置（个人尝试直接用ln连接创建sublime连接时会提示报错）
![报错](http://upload-images.jianshu.io/upload_images/5796375-73feaf02416ab239.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

      用vim 打开~/.bashrc进行编辑，在任意位置（最好是顶部）添加下面一行即可，然后执行source ~/.bashrc，使得修改生效即可。
```
alias sublime='subl'
```
![.bashrc中的修改](http://upload-images.jianshu.io/upload_images/5796375-4adb7daf6e62c466.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 执行命令流程图

![流程图](http://upload-images.jianshu.io/upload_images/5796375-35f325fda24fc778.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 注意
1. 使用软连接，而不是使用硬链接，软连接相当于一个快捷方式，而硬链接相当于创建了一个指向该可直接文件的指针，当卸载sublime后，由于还有指针指向该文件，因此依旧可以访问这个可执行文件。

2. 尽可能将自己创建的命令放置在/usr/local/bin/目录下，因为/bin和/sbin存放的是操作系统的可执行命令，/usr/bin和/usr/sbin存放的是应用软件的命令。而/usr/local/bin/可以存放用户自定义的命令。

3. 确保你链接路径在PATH变量中，否则无法通过PATH查找到可执行命令。

2.
