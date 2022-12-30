---
title: React 状态管理
slug: managing-react-state
category: tech
date: 2018-03-27 00:02:38
updated:
tags: [React]
isCJKLanguage: true
from: http://maksimivanov.com/posts/react-state
---

React 中的 `state`（状态）是简单的 JavaScript 对象，用于管理组件内的状态。请牢记：只有用在渲染（render）过程中的数据才保存在状态里。

## 什么是状态（State）

React 组件用状态来保存相关数据，它本身就是一个简单的对象。状态和普通对象的区别在于，React 会监视状态的属性，并在它更新时触发组件重新渲染。

状态更新会触发重新渲染，因此可以只用它来保存渲染所需的数据。换句话说，虽与组件相关但不用于 `render` 方法的变量就无需保存在状态里了，使用实例变量即可。

同时，你还可以认为状态就是组件的私有数据，只有在组件内才能读取或更新状态。我们不能在它的父组件或子组件里访问状态。

## 与属性（Props）的区别

和 `props` 类似，状态就是一个用于触发组件重新渲染的对象。区别在于 `props` 来自父组件，状态是组件的内部数据。

我们无法在组件内更新 `props`。因为它是来自外部的数据，组件不能对其进行操作。状态处于组件内部，组件对它有完全操作的能力。

如下图：

![state vs props](/img/state_vs_props.png)

## 初始化状态

我们有多种方式来初始化组件的状态：

- 使用 `getInitialState` 方法
- 在 `constructor` 方法内初始化
- 以类属性的方式初始化

#### 使用 `getInitialState` 方法

使用 `React.createClass` 来定义组件时，可以使用 `getInitialState` 方法：

```jsx
import React from 'react';

const ExampleComponent = React.createClass({
  getInitialState () {
    return {
      someKey: 'someValue'
    };
  },

  render() {
    return (
      <div>{this.state.someKey}</div>
    );
  }
});

export default ExampleComponent;
```

#### 在 `constructor` 方法内初始化

当你使用 `ES6` 的 Class 关键字定义组件时，可以在 `constructor` 方法里初始化状态：

```jsx
import React from 'react';

class ExampleComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      someKey: 'someValue'
    };
  }
  render() {
    return (
      <div>{this.state.someKey}</div>
    );
  }
}

export default ExampleComponent;
```

请不要忘记调用 `super()` 方法，否则在 `constructor` 内将无法使用 `this`。不要漏掉了 `super` 方法里的 `props` 参数。

#### 以类属性的方式初始化

同样在使用 `ES6` 的 Class 关键字时，我们还可以用类属性的方式初始化状态。这种写法可以节省几行代码：

```jsx
import React from 'react';

class ExampleComponent extends React.Component {
  state = { someKey: 'someValue' }

  render() {
    return (
      <div>{this.state.someKey}</div>
    );
  }
}

export default ExampleComponent;
```

## 更新状态

更新状态时，需要留意如下规则：

- 不要直接修改状态的属性值
- 警告：`setState` 是一个异步方法
- 状态更新会合并操作

#### 不要直接修改状态的属性值

第一条规则就是「不要向任何人提起状态更新」，恩不对，好像进错片场了。第一条规则是：不要直接修改状态的属性值。

```jsx
// 不要这样做
this.state.someValueInState = 'NEW VALUE';
```

我们只能在初始化状态时直接写入状态，比如在 `constructor` 里进行定义。

除了初始化之外，我们只能使用 `this.setState` 方法来更新状态：

```jsx
this.setState({someValueInState: 'NEW VALUE'});
```

#### 警告：`setState` 是一个异步方法

请留意 `setState` 的两个关键点：第一，在计算下一个状态时不能依赖 `this.state` 和 `this.props`，它们可能会被异步更新：

```jsx
// 不要这样做
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

在这种情况下，用 `function` 作为 `setState` 的第一个参数即可：

```jsx
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

第二，由于 `setState` 是异步执行的，因此我们不能依赖 `this.setState` 之后马上取得的 `this.state` 值。

如果你要在状态真正完成更新时执行代码，请使用 `this.setState` 的第二个参数，回调函数：

```jsx
this.setState({
    someKey: 'someValue',
  }, () => {
    // 只有在状态完成更新后，才会执行这里的代码
  }
);
```

#### `state` 更新会合并操作

当我们调用 `setState` 方法时，React 将把参数传递的对象合并到当前的状态中。我们不必担心会出现属性覆盖的情况。

假设我们在 `constructor` 中定义了一个状态：

```jsx
constructor(props) {
  super(props);
  this.state = {
    someKey: 'someValue',
    someOtherKey: 'someOtherValue'
  };
}
```

之后，我们可以用 `setState` 分别更新这两个属性：

```jsx
this.setState({
  someKey: 'someNewValue'
});
```

在上面的代码里，`this.state.someOtherKey` 就不会被覆盖。
