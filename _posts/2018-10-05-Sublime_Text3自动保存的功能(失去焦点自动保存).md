---
layout: post
title:  "Sublime-Text3自动保存的功能(失去焦点自动保存)"
date:   2018-10-05 20:20:29 +0800
categories: Tool
---

### 第一步
preferences 下面的settings；(和老版本的不一样了吧,之前有什么default 和 users,这里只有settings)
![mac 版本settings文件位置](https://upload-images.jianshu.io/upload_images/5796375-db92f4e1c51ebd87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 第二步
现在分两边了,左边文件是默认配置，右边文件是用户自定义配置，左边是只读的不能编辑,在左边ctrl + f ,然后在下面输入框里输入save_on_focus_lost，在右边文件中设置为true。保存文件即可。
![屏幕快照 2018-03-28 14.29.16.png](https://upload-images.jianshu.io/upload_images/5796375-7f6bfd9a787d2fd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

