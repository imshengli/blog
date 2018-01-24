---
title: Redux
date: 2018-01-24 09:25:10
categories: JavaScript
tags:
  - js
---

## 概述

Redux 适用场景：多交互、多数据源。

> 从组件角度看：
> 某个组件的状态，需要共享；
> 某个状态需要在任何地方都可以拿到；
> 一个组件需要改变全局状态；
> 一个组件需要改变另一个组件的状态；

设计思想：

> 1）Web 应用是一个状态机，视图与状态是一一对应的。
> 2）所有的状态，保存在一个对象里面。


```js
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import Counter from './components/Counter'
import counter from './reducers'

// store
// Store 就是保存数据的地方，整个应用只能有一个 Store。
// state 某时刻的 store 的快照；
// state = store.getState()
// 获取当前时刻的 State
// createStore 函数接受另一个函数作为参数，返回新生成的 Store 对象。
const store = createStore(counter)
const rootEl = document.getElementById('root')

// action
// Action 描述当前发生的事情
// action 是 view 发出的通知；
// store.dispatch()是 View 发出 Action 的唯一方法。

// Store 收到 Action 以后，必须给出一个新的 State，
// 这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。
// store.dispatch 方法会触发 Reducer 的自动执行

const render = () => ReactDOM.render(
  <Counter
    value={store.getState()}
    onIncrement={() => store.dispatch({ type: 'INCREMENT' })}
    onDecrement={() => store.dispatch({ type: 'DECREMENT' })}
  />,
  rootEl
)

render()

// store.subscribe方法设置监听函数
// 一旦 State 发生变化，就自动执行这个函数。
store.subscribe(render)
```

## 总结

首先，用户发出 Action。

```js
store.dispatch(action);
```

然后，Store 自动调用 Reducer，并且传入两个参数：当前 State 和收到的 Action。
Reducer 会返回新的 State 。

```js
let nextState = todoApp(previousState, action);
```

State 一旦有变化，Store 就会调用监听函数。

```js
// 设置监听函数
store.subscribe(listener);
```

listener可以通过store.getState()得到当前状态。
如果使用的是 React，这时可以触发重新渲染 View。

```js
function listerner() {
  let newState = store.getState();
  component.setState(newState);
}
```

## 参考文档

- [Redux 入门教程（一）：基本用法](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)
