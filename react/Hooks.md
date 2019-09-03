### 什么是Hook?
  - Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。
  - Hook 本质就是 JavaScript 函数，是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。但是在使用它时需要遵循两条规则。      
     - **只在最顶层使用 Hook, 不要在循环、条件或嵌套函数中调用 Hook**， 确保总是在 React 函数的最顶层调用他们。
     -  **只在 React 函数中调用 Hook, 不要在普通的 JavaScript 函数中调用 Hook。**
        - 在 React 的函数组件中调用 Hook
        - 在自定义 Hook 中调用其他 Hook   

  另外， Hook 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。 Hook调用机制，是靠顺序调用的。

### 没有破坏性改动
  - **完全可选的。** 无需重写任何已有代码就可以在一些组件中尝试 Hook。
  - **100% 向后兼容的。** Hook 不包含任何破坏性改动。
  - **现在可用。**  Hook 已发布于 v16.8.0。
  - **没有计划从 React 中移除 class。**
  - **Hook 不会影响你对 React 概念的理解。** 恰恰相反，Hook 为已知的 React 概念提供了更直接的 API：`props`， `state`，`context`，`refs` 以及生命周期。

### Hook出现的动机
  - **在组件之间复用状态逻辑很难**   
     **React 没有为共享状态逻辑提供更好的原生途径。** 虽然有一些解决此类问题的方案，比如 `render props` 和 `高阶组件`。但是这些方案需要重新组织你的组件结构，这可能很麻烦，会使代码很难理解。如果在React devTools中查看，会发现 `providers`, `consumers`, `高阶组件`，`render props` 等其他抽象层组成的组件会形成`“嵌套地狱”`。      
     你可以使用 Hook 从组件中提取状态逻辑，使得这些逻辑可以单独测试并复用。**Hook 使你在无需修改组件结构的情况下复用状态逻辑。** 这使得在组件间或社区内共享 Hook 变得更便捷。

  - **复杂的组件变的难以理解**  
    起初我们的组件看起来很简单，随页面和业务的增加，逐渐会被状态逻辑和副作用充斥。每个生命周期常常报刊一些不相关的逻辑，例如，组件常常在 `componentDidMount` 和 `componentDidUpdate` 中获取数据。但是，同一个 `componentDidMount` 中可能也包含很多其它的逻辑，如设置`事件监听`，而之后需在 `componentWillUnmount` 中清除。相互关联且需要对照修改的代码被进行了拆分，而完全不相关的代码却在同一个方法中组合在一起。如此很容易产生 `bug`，并且导致逻辑不一致。

    为了解决这个问题，**Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据），而并非强制按照生命周期划分。** 你还可以使用 reducer 来管理组件的内部状态，使其更加可预测。

  - **难以理解的 class**   
    除了代码复用和代码管理会遇到困难外,我们还发现 class 是学习 React 的一大屏障。
    你必须去理解 `JavaScript` 中 `this` 的工作方式，这与其他语言存在巨大差异。还不能忘记绑定事件处理器。  
    另外，React 已经发布五年了，React团队希望它能在下一个五年也与时俱进。 就像 [Svelte](https://svelte.dev/)，[Angular](https://angular.io/)，[Glimmer](https://glimmerjs.com/)等其它的库展示的那样。    
    `class` 也给目前的工具带来了一些问题, 我们发现 `class` 组件会无意中鼓励开发者使用一些让优化措施无效的方案。例如，`class` 不能很好的压缩，并且会使热重载出现不稳定的情况。

### 渐进策略
  - **没有计划从 React 中移除 class。**
  - **Hook 和现有代码可以同时工作，你可以渐进式地使用他们。**

---

### 使用 State Hook

```js
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  
  // 等号左边名字并不是 React API 的部分，你可以自己取名字。
  // useState可以传参数
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p> // 读取state
      <button onClick={() => setCount(count + 1)}> // 更新state
        Click me
      </button>
    </div>
  );
}
```
**等价的class 示例**
```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    // 声明一个叫 "count" 的 state 变量
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p> // 读取
        <button onClick={() => this.setState({ count: this.state.count + 1 })}> // 更新state
          Click me
        </button>
      </div>
    );
  }
}
```

### 使用 Effect Hook
```js
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // 与componentdidmount和componentdiddupdate类似
  useEffect(() => {
    // 使用浏览器API更新文档标题
    document.title = `You clicked ${count} times`;
  });

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
总结：  

  - 如果你熟悉 React class 的生命周期函数，你可以把 useEffect Hook 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。**不同的是，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕**。
  - useEffect 会在每次渲染后都执行。默认情况下，它在第一次渲染之后和每次更新之后都会执行。
  - **在 React 组件中有两种常见副作用操作**：需要`清除的`和`不需要清除的`。  
    -  **不需要清除**，比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。另外，第二个参数可以不传。
        ```js
          useEffect(() => {
            document.title = `You clicked ${count} times`;
          });
        ```
    - **需要清除** ，比如订阅外部数据源、绑定事件等。要在 effect 中返回一个函数，这是 effect 可选的清除机制。
        ```js
          iuseEffect(() => {
            function handleStatusChange(status) {
              setIsOnline(status.isOnline);
            }

            ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
            return () => {
              ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
            };
          });
        ```
  - **通过跳过 Effect 进行性能优化**
    - 如果某些特定值在两次重渲染之间没有发生变化，通知 React 跳过对 effect 的调用， 只要传递数组作为 useEffect 的第二个可选参数即可
        ```js 
        useEffect(() => { 
          document.title = `You clicked ${count} times`;
        }, [count]);// 仅在 count 更改时更新
        ``` 
    -  只需要执行一次，第二个参数可传为空数组[];
  -  **清除 effect**  
      - **为什么要在 effect 中返回一个函数？** 这是 effect 可选的清除机制。每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。
      - **React 何时清除 effect？** React 会在组件卸载的时候执行清除操作。
  - **使用多个 Effect 实现关注点分离**   
    使用 Hook 其中一个目的就是要解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。
    ```js
      function FriendStatusWithCounter(props) {
        const [count, setCount] = useState(0);
        useEffect(() => {
          document.title = `You clicked ${count} times`;
        });

        const [isOnline, setIsOnline] = useState(null);
        useEffect(() => {
          function handleStatusChange(status) {
            setIsOnline(status.isOnline);
          }

          ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
          return () => {
            ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
          };
        });
        // ...
      }
    ```
    **Hook 允许我们按照代码的用途分离他们，** 而不是像生命周期函数那样。React 将按照 effect 声明的顺序依次调用组件中的每一个 effect。  
    我们发现 effect 的清除机制可以避免了 `componentDidUpdate` 和 `componentWillUnmount` 中的重复，同时让相关的代码关联更加紧密，帮助我们避免一些 `bug`。我们还看到了我们如何根据 `effect` 的功能分隔他们，这是在 `class` 中无法做到的。

### 自定义Hook
  将两个函数之间一些共同的代码提取到单独的函数中。
  ```js
    import React, { useState, useEffect } from 'react';

    function useFriendStatus(friendID) {
      const [isOnline, setIsOnline] = useState(null);

      useEffect(() => {
        function handleStatusChange(status) {
          setIsOnline(status.isOnline);
        }

        ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
        return () => {
          ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
        };
      });

      return isOnline;
    }
  ```
  在 `FriendStatus` 和 `FriendListItem` 组件中使用：
  ```js
  function FriendStatus(props) {
    const isOnline = useFriendStatus(props.friend.id);

    if (isOnline === null) {
      return 'Loading...';
    }
    return isOnline ? 'Online' : 'Offline';
  }

  function FriendListItem(props) {
    const isOnline = useFriendStatus(props.friend.id);

    return (
      <li style={{ color: isOnline ? 'green' : 'black' }}>
        {props.friend.name}
      </li>
    );
  }
  ```
  **注意：**  
  
  - **自定义 Hook 必须以 “use” 开头。** 必须如此。这个约定非常重要。不遵循的话，由于无法判断某个函数是否包含对其内部 Hook 的调用。

  - **在两个组件中使用相同的 Hook 会共享 state 吗？** 不会。自定义 Hook 是一种重用状态逻辑的机制(例如设置为订阅并存储当前值)，所以每次使用自定义 Hook 时，其中的所有 state 和副作用都是完全隔离的。

  - **自定义 Hook 如何获取独立的 state？** 每次调用 Hook，它都会获取独立的 state。
