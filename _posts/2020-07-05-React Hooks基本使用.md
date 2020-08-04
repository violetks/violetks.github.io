---
layout:     post
title:      React Hooks基本使用
date:       2020-07-05
author:     violetks
header-img: img/tag-bg-o.jpg
catalog: true
tags:
    - React
---

> Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。<br>

### 一、useState

```javascript
const [state, setState] = useState(initialState);
```

用于在函数组件中定义 `state变量`，React 会在重复渲染时保留这个 `state`。<br>
`useState` 传入参数是 `state` 的初始值，并返回一个数组：当前状态和一个更新它的函数，你可以在事件处理函数中或其他一些地方调用这个函数。<br>

```javascript
import React, { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
       Click me
      </button>
    </div>
  );
}
```

### 二、useEffect

```javascript
useEffect(() => {})
```

`useEffect` 和React的生命周期相关，可看作是 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。<br>
在执行DOM更新之后调用。默认情况下，每次渲染都会执行。** `effect` 是异步的。`useEffect` 会延迟执行。**<br>

**清除机制：**如果 `effect` 返回一个函数，React 将会在执行清除操作时调用它：<br>

```javascript
useEffect(() => {
  ...
  return cleanup() => {...}
})
```

**useEffect 的第二个参数：**是一个数组，传入副作用函数中使用到的数据变量，主要用于性能优化，防止进行重复渲染。依赖项数组不会作为参数传给 effect 函数。虽然从概念上来说它表现为：所有 `effect` 函数中引用的值都应该出现在依赖项数组中。<br>

```javascript
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新，count发生变化时就会重新渲染

useEffect(() => {
  getUserList();
}, []); // 定义为空数组，只在挂载时渲染一次
```

### 三、useContext

```javascript
const value = useContext(MyContext);
```

### 四、useReducer

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

`useState` 的替代方案。适用于 `state` 逻辑较复杂且包含多个子值，或者下一个 `state` 依赖于之前的 `state` 等。<br>

```javascript
// 计数器
function reducer(state, action) {
  switch (action.type) {
    case 'increment':return state + 1;
    case 'decrement':return state - 1;
    default:throw new Error();
  }
}

function Counter() {
  const [count, dispatch] = useReducer(reducer, 10);
  return (
    <>
      Count: {count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### 五、useMemo

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

在 React 中，每次父组件更新时，子组件也会自动更新。`useMemo` 和 `useCallback` 都会在组件第一次渲染时执行，之后会在其依赖的变量发生改变时再次执行。<br>
**`useMemo` 返回缓存的变量。**<br>
把“创建”函数和依赖项数组作为参数传入 `useMemo`，它仅会在某个依赖项改变时才重新计算 `memoized` 值，阻止子组件一些不必要的更新。<br>

### 六、useCallback

```javascript
const fnA = useCallback(fnB,[a])
```

**`useCallback` 返回缓存的函数。**<br>
使用场景：子组件接收父组件的一个函数作为props，父组件更新时，子组件里的函数没必要更新，就可以通过 `useCallback` 来返回函数。<br>

### 七、useRef

```javascript
const refContainer = useRef(initialValue);
```

`useRef` 像一个变量，类似于 `this`，`useRef` 返回一个可变的 `ref` 对象。<br>

#### useRef获取DOM元素

```javascript
function Example() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.value = "Hello";
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>在input上展示文字</button>
    </>
  );
}
```

### 八、自定义 Hook

**自定义 Hook 是一个函数，其名称以 “use” 开头，函数内部可以调用其他的 Hook。**<br>

![aGhxUA.png](https://s1.ax1x.com/2020/08/01/aGhxUA.png)

