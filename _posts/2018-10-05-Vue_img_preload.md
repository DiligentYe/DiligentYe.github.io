---
layout: post
title:  "Vue-img-preload"
date:   2018-10-05 20:20:29 +0800
categories: Vue
---

## vue-img-preload
  预加载页面上的图片资源，提高用户体验

  ![效果预览](https://upload-images.jianshu.io/upload_images/5796375-b029c649855a5d67.gif?imageMogr2/auto-orient/strip)

### 使用方法
##### 浏览器中
1. 下载vue-img-preload插件
```
npm install vue-img-preload
```
2. 在页面中引入dist目录下的**preload.min.js**文件**（确保此时页面已经引入vue.js）**，如
```
<script src="path/dist/preload.min.js"></script>
```
3. 首先创建一个空的Vue实例，用于页面中的事件监听。调用Vue.use方法装载Preload插件，并传入配置参数，eachLoaded在每张图片下载完成后都会执行该方法， allLoaded则在所有图片都下载完成后执行，此处分别触发一个eachload，allload事件，并传回部分内部处理参数，如已下载图片数量，图片下载完成后的event对象。
```
   // 非父子组件通信
    var bus = new Vue()
    // 安装插件
    Vue.use(Preload, {
        eachLoaded: function(msg) {
            bus.$emit('eachload', msg)
        },
        allLoaded: function(msg) {
            bus.$emit('allload'，msg)
        }
    })
```
4. 此时Vue对象中添加一个v-preload指令，可以在Vue管理的结构中使用，**<div v-preload="imgs"></div>**，并传入参数。
```
// 页面结构
<div id="app">
        <div class="spinner" :class="[isLoaded ? 'loading' : '']">
            <div class="rect1"></div>
            <div class="rect2"></div>
            <div class="rect3"></div>
            <div class="rect4"></div>
            <div class="rect5"></div>
        </div>
        <div v-preload="imgs"></div>
        <div class="pics">
            <img v-for="img in imgs" :src="img" alt="" width=250 />
        </div>
    </div>
```
```
// 数据
 var vm = new Vue({
        el: '#app',
        data: function() {
            return {
                isLoaded: false,
                imgs: [
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_0923.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3606.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3657.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3715.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3775.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3789.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3791.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3832.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3833.JPG?raw=true',
                    'https://github.com/DiligentYe/some-picture/blob/master/IMG_3910.JPG?raw=true'
                ]
            }
        }
    })

    // 注册eachload，allload事件处理函数，当所有图片都下载完毕后，通过控制isLoaded的值，控制loading类，从而控制加载动画
    bus.$on('eachload', function(msg) {
        console.log(msg.total)
    })

    bus.$on('allload', function(msg) {
        vm.isLoaded = true
    })
```
**注：可运行vue-img-preload/test/web/simple.html查看效果**
4. 上述方法中，在装载插件的时候统一配置eachLoaded，和allLoaded配置项，如果针对页面中多个部分分别进行配置，此时需要给v-preload传入一个Object参数，其中包含imgs和options属性，分别对应图片地址数组和特定配置项，如下:
```
infos: {
     imgs: ['https://github.com/DiligentYe/some-picture/blob/master/IMG_0923.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3606.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3657.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3715.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3775.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3789.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3791.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3832.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3833.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_3910.JPG?raw=true'],
     options: {
         allLoaded: function(msg) {
             bus.$emit('allload')
         }
     }
 }

datas: {
     imgs: ['https://github.com/DiligentYe/some-picture/blob/master/IMG_4030.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4056.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4967.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4968.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4969.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4970.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4971.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4972.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4973.JPG?raw=true', 'https://github.com/DiligentYe/some-picture/blob/master/IMG_4974.JPG?raw=true'],
     options: {
         type: 'order ',
         eachLoaded: function(msg) {
             bus.$emit('eachload', msg)
         },
         allLoaded: function(msg) {
             bus.$emit('allload')
         }
     }
 }
```
针对infos，只会在所有图片下载完成的时候触发allload，而datas，则设置了图片有序下载，并且单张下载完毕和所有图片下载完毕都会执行相应的动作。
**注：可运行vue-img-preload/test/web/index.html查看效果**

##### 组件中
大体用法与在页面中一致，具体用法可参考**vue-img-preload/test/module/test.js**写法，运行结果可运行**npm run dev**，启动一个服务器，访问对应地址即可。

### 配置参数
1. eachLoaded(function): 每次加载成功图片的行为，默认在控制台打印**one picture has been loaded**。

2. allLoaded(function): 所有图片加载成功的行为，默认在控制台打印**all picture has been loaded**。

3. type(string): 图片有序／无序加载，默认为’disorder‘，采用无序加载，如果type值不为’disorder‘，则采用有序加载。使用有序加载时，建议设置为‘order’。

4. max: 一次加载的照片个数，默认值为1000，当图片数量小于max时，加载所有图片；当图片的数量大于max时，采用分块加载，当前块加载完毕后才会加载下一块，每块的长度为max


