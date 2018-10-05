---
layout: post
title:  "webpack vue项目中配置ESLint"
date:   2018-10-05 20:20:29 +0800
categories: Vue
---

使用ESLint对代码书写进行规范，可以有效的提高代码的质量，降低代码出错的机率，因此有必要将ESLint引入到项目中。

目前网上已经存在标准的ESLint配置，该规范是目前业界比较推崇的代码编写规范，以下是配置方法：

#### 1. 安装ESLint标准配置
```
npm install --D eslint-config-standard eslint-plugin-standard eslint-plugin-promise eslint-plugin-import eslint-plugin-node
```
其中：eslint-config-standard是依赖于后面的plugin

#### 2. 编写配置文件
在项目根目录下添加.eslintrc文件，该文件是一个JSON配置文件，需要在该配置文件中指定"extends"字段为"standard"，指明继承标准配置文件。
配置文件内容如下：
```
{
  "extends": "standard"
}
```
此时在package.json中的scripts字段中配置"lint"命令，如下
```
"lint": "eslint --ext .js --ext .vue src/"
```
调用本地的eslint命令，用--ext指明需要检测js文件和vue文件，最后指定检测的文件所在目录为src/。
__注意：在package.json中scripts字段中调用eslint命令使用项目中的eslint命令，如果想要在全局中调用eslint命令，需要全局安装。__
调用后结果如下：
![运行结果](https://upload-images.jianshu.io/upload_images/5796375-1e57b0602e0a8c12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为什么会出错呢？
此时报错的都是vue文件，由于vue文件的书写格式特殊，所以需要配置后才能正确处理。

#### 3. 配置Vue ESLint
Vue文件各个部分是由标签包裹的，因此编写规范同html一致。此时，需要下载eslint-plugin-html插件，并且在eslint的配置文件中指明使用插件。
插件下载命令
```
npm install -D eslint-plugin-html
```
此时配置文件修改为：
```
{
  "extends": "standard",
  "plugins": [
    "html"
  ]
}
```
此时在运行，便没有问题（前提条件是代码书写确实符合规范）
![运行结果](https://upload-images.jianshu.io/upload_images/5796375-87c0deed933847cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4. 在webpack中配置ESLint
我们想要达到的目的是，在我们的代码编写过程中，webpack可以实时对代码进行检测，并给出提示。
在webpack＋vue的项目中，我们编写的代码都需要通过babel编译，编译后的代码并不能满足eslint的规范，因此，我们需要对编译前的文件进行检查。这时，我们需要使用babel-eslint编译器对文件进行检查（否则无法识别新特性或者特殊文件的编写格式，如vue）。
安装指令如下
```
npm install babel-eslint -D
```
修改.eslintrc如下
```
{
  "extends": "standard",
  "plugins": [
    "html"
  ],
  "parser": "babel-eslint"
}
```
指明解析器为babel-eslint

在webpack中，提供了eslint-loader支持对特定文件进行代码格式检查，因此，首先我们需要下载eslint-loader，然后在webpack配置文件中配置规则。
```
{
  "extends": "standard",
  "plugins": [
    "html"
  ],
  "parser": "babel-eslint"
}
```
```
npm install eslint-loader -D
```
webpack中module.rules中新增一项规则
```
{
        test: /\.(vue|js)$/,
        loader: 'eslint-loader',
        exclude: /node_modules/,
        // 预处理
        enforce: 'pre'
}
```
这里指明了对js和vue文件使用eslint-loader进行处理，并排除了/node_modules/目录中的文件，并利用enforce字段指明，该loader是进行预处理的loader，先对指定文件进行eslint后才会执行babel编译。

以上便可完成了所有配置。

____
#### 代码提交之前对代码进行检查
我们在github托管代码时，会将自己的代码提交到github仓库中，如果我们可以在每一次提交之前对我们的代码进行检查，确定无误后才正确提交，这样的话，就能保证线上的代码也是合乎规范的，这样有利于代码的维护。

如何实现以上功能呢？

这里我们需要借助husky工具，安装完该工具之后，该工具可以在我们提交代码时，调用"precommit"钩子，执行预处理操作。

首先安装该工具
```
npm install -D husky
```

然后在package.json的scripts字段中配置precommit，让其执行代码检测操作
```
 "precommit": "eslint --ext .js --ext .vue src/"
```
此时提交的代码存在问题，就会报错，提交失败。

____
#### 统一编辑器设置
目前网上存在很多编辑器，其自定义配置也是各不相同，如何统一编辑器的设置呢？

所有的编辑器都提供了editorconfig的插件，它会读取项目根目录下的.editorconfig文件，然后进行配置，因此我们可以通过.editorconfig文件来编写编辑器的配置方案。
```
# 标识为最终配置
root = true

# 所有文件配置
[*]
# 字符集
charset = utf-8
# 行结束符号
end_of_line = lf
# 缩紧
indent_size = 2
# 缩紧方式
indent_style = space
# 最后插入一行
insert_final_newline = true
# 删除最后的空格
trim_trailing_whitespace = true
```





