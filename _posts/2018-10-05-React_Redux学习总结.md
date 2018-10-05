---
layout: post
title:  "React Redux学习总结"
date:   2018-10-05 20:20:29 +0800
categories: React
---

### 1. React
React是一个View层的库，可用于组件化开发。该库提供一个JSX语法，允许用户在JS文件像写HTML一样，编写组件的结构，并根据一系列的语法，将数据嵌入所写的HTML中。

1. 基本用法
React框架对用户提供了两个类库，react和react-dom，在使用前需要引入这两个包。其中react提供Component，自定义组件继承Component；react-dom则对外提供了render方法，将组件挂载到页面中。

```
class App extends React.Component {
  render() {
    return (
      <div>Hello React</div>
    )
  }
}
```

```
ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

2. 内部状态state和外部参数prop
__state__: 组件内部状态，在constructor构造函数中通过设置this.state的值初始化，此后利用this.state进行获取，并利用this.setState函数更新值（__注意：每次都需要返回一个新的对象或者值__）
__prop__: 父组件在调用子组件通过设置属性将数据参入子组件内部，在子组件中通过this.props获取

以上两种数据改变都会触发组件的更新操作。

3. 事件
通过在JSX中的标签上设置onClick，onChange等属性，将触发后的事件处理函数作为值。
```
class App extends React.Component {
  render() {
    return (
      <div>
        <button onClick={add}>增加</button>
      </div>
    )
  }
}
```
通常情况下，事件处理函数执行后，都会改变现有的state，触发组件更新操作。
__注意：__事件处理函数异步调用时是在全局环境中执行，因此需要绑定this，方法如下
```
1. 箭头函数: () => {}
2. 在constructor中绑定: this.add = this.add.bind(this)
```

4. React存在的问题
为了统一管理状态数据，通常会将数据放在最上层组件中，然后通过props层层传递，传递到底层组件中。如果嵌套太深，参数传递过程非常的复杂。因此出现了Redux。

### 2. Redux
1. 基本用法
Redux中存在几个概念：state, action, dispatch

__state:__ 依旧是组件的内部的状态
__action:__ 组件动作，相应的改变组件内部的状态值
__dispatch:__ 发出相应的动作

Redux中提供createStore方法用于生成一个store对象，这个函数接受一个初始值state值和一个reducer函数。当用户发出相应的action时，利用传入的reducer函数计算出一个新的state值，并返回。

store对象中包括getState方法(获取目前的state)，subscribe方法(指定监听函数，当state变化时调用)，dispatch方法(分发action)。

在使用过程中，需要将store作为参数传递给子组件。

```
// reducer
function counter(state = 0, action) {
  switch (action.type) {
    case 'ADD':
      return state + 1;
    case 'SUB':
      return state - 1;
    default:
      return 10;
  }
}

// 创建Store
let store = createStore(counter);
console.log(store.getState()); // 10

// 监听器
function listener() {
  console.log(store.getState()) // 先输出11，在输出10
}

// 订阅事件监听
store.subscribe(listener);

// 分发事件
store.dispatch({ type: 'ADD' });

store.dispatch({ type: 'SUB' });
```

2. react-redux
react-redux是为了方便开发，提供了一个Provider组件，以及connect方法。Provider组件作为做上层组件，需要将store作为参数注入组件中，此后在子组件中都可以访问到store这个对象；connect方法接受两个参数：mapStateToProps，actionCreators，并返回处理后的组件，其中mapStateToProps可以将对应的state作为prop注入对应的子组件，actionCreator可以将对应的actioncreator作为prop注入对应的子组件。
```
// 定义Connect,将对应的数据注入
const mapStateToProps = (state) => {
  return { num: state }// 必须返回一个对象
};
const actionCreator = { addAction, subAction, addActionAsync }

App = connect(mapStateToProps, actionCreator)(App)
```

__装饰器写法：__
```
@connect(
  (state) => ({ num: state.counter }),
  { addAction, subAction, addActionAsync }
)
```
使用上述写法，需要安装babel插件babel-plugin-transform-decorators-legacy，并且在babelrc中添加相应的配置
```
 npm install --save-dev babel-plugin-transform-decorators-legacy
```
```
 "plugins": [
   "transform-decorators-legacy"
]
```

### 3. React-router
react-router提供了web和Native两个版本，当在浏览器中使用时，需要安装react-router-dom
```
npm install --save react-router-dom
```
react-router-dom中提供了BrowserRouter, Link, Route, Switch，Redirect。
其中：
    BrowserRouter包裹所有需要路由控制的内容，在使用redux时，需要放置在Provider组件中。
    Link组件用于链接某个路径下，用户点击跳转
    Route组件用于路由到这个组件
    Switch组件用于在内部Route组件中选一个
    Redirect组件用于重定向到某个路径下
```
<Provider store={store}>
    <BrowserRouter>
      <Switch>
        <Route path="/" exact component={Dashboard}></Route>
        <Route path="/login" component={Auto}></Route>
        <Route path="/dashboard" component={Dashboard}></Route>
        <!-- <Redirect to="/dashboard"></Redirect>     -->
        <!-- <Link to="/dashboard"></Link> -->
      </Switch>
    </BrowserRouter>
  </Provider>,
```

### 4. 多个reducer
当存在多个reducer，分别管理不同方面的state，需要将其合并成一个reducer，redux中提供了combineReducers函数，完成该项功能。
```
combineReducers({ counter, auth }) //返回合并后的reducer
```


### 5. React-devtools Redux-devtool安装
这两个插件可直接在Chrome商店中安装，React-devtools可直接使用，Redux-devtool则需要在代码中进行相应的设置才能正常使用。
```
// 配置Redux-devtools

const composeEnhancers =
  typeof window === 'object' &&
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({
      // Specify extension’s options like name, actionsBlacklist, actionsCreators, serialize...
    }) : compose;

const enhancer = composeEnhancers(
  applyMiddleware(thunk),
  // other store enhancers if any
);

let store = createStore(reducers, enhancer);
```






