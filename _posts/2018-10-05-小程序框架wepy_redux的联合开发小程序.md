---
layout: post
title:  "小程序框架wepy redux的联合开发小程序"
date:   2018-10-05 20:20:29 +0800
categories: 小程序
---

### redux基础
在redux整体框架中，由store统一管理系统数据，因此，需要先设计action，reducer，然后由reducer创建store。

其中：
action通过action creator函数生成，action creator函数则是一个返回包含type字段的对象。
reducer则是用于根据dispatch的action类型的生成新的state的纯函数。
redux提供createStore方法接收一个reducer，返回一个store对象。该对象中包含getState，dispatch方法，用于获取内部状态state和发送action。
```
// action Type:
const INCREMENT = 'INCREMENT'
const DECREMENT = 'DECREMENT'

// action:
function increment(payload) {
  return {
    type: INCREMENT
  }
}

function decrement(payload) {
  return {
    type: DECREMENT
  }
}

// reducer:
function counter(state = 0, action) {
  switch (action.type) {
    case INCREMENT:
      return state + 1
    case DECREMENT:
      return state - 1
    default:
      return 0
  }
}

// store:
const store = createStore(combineReducers({ counter }))

// 获取state
console.log(store.getState())

// 派发action
store.dispatch({type: 'INCREMENT'})

// 获取更新后的state
console.log(store.getState())
```

以上生成整个框架中所需的store对象。

### 连接wepy和redux
连接react和redux之间提供了react-redux包，对外提供了Provider组件，将store传递到子页面（组件）中，提供connect方法，将state和action注入到子页面（组件）中。

对于wepy，同样提供了wepy-redux包，对外提供setStore，getStore，以及connect方法。
setStore是将store注入到小程序的所有页面中；
getStore用于在页面中获取Store对象；
connect将state和actions注入到子页面（组件）中；

```
// 在app.wpy中将store注入页面（组件中）
setStore(store)

// 在其他页面中使用connect将页面所需的数据注入到页面中
<template>
  <view id="root">Hello Wepy!{{counter}}</view>
  <button @tap="increment">增加</button>
  <button @tap="decrement">减少</button>
</template>

<script>
import wepy from 'wepy'
import { connect } from 'wepy-redux'

import { increment, decrement } from '../store/actions'

@connect({ // state
  counter(state) { // 注入counter
    return state.counter
  }
},
  { // action Creator
    increment, 
    decrement
  })

export default class Index extends wepy.page {

}
</script>
```

__注意：__使用connect方法将states和actions注入到页面(组件)中时，是将states注入到小程序的computed属性中，将actions注入到methods属性中，在js逻辑中可以通过this.XXX和this.methods.XXX获取注入的state和action。

![](https://upload-images.jianshu.io/upload_images/5796375-c371a2ae9800dda2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

__wepy-redux地址：__[https://www.npmjs.com/package/wepy-redux](https://www.npmjs.com/package/wepy-redux)

















