## React子父组件之间如何传值

### 1. 父组件向子组件
父组件可以通过传递props的方式，从上往下的向子组件进行通讯。

### 2. 子组件向父组件
同样也需要父组件向子组件传递props进行通讯，只是父组件传递的，是作用域为父组件自身的函数，子组件调用该函数，将子组件想要传递的信息，通过参数传递到父组件的作用域中。

### 3. 兄弟组件通讯
对于没有直接关联的两个子组件，它们唯一的共同点就是拥有相同的父组件，可以先通过子组件1向父组件传递，再由父组件向子组件2传递通讯。
然而，这个方法有个问题，就是父组件的state变化，会触发父组件及下面的子组件的生命周期，比如componentDidUpdate的更新。下面就是针对这种组件层级比较深的解决方案。

### 4. 观察者模式
观察者模式也叫 ***发布者-订阅者模式***，发布者发布事件，订阅者监听事件并做出反应。

### 5. createContext
**Context** 提供了一个无需为每层组件手动添加 `props`，就能在组件树间进行数据传递的方法。Context 设计目的是为了共享那些对于一个组件树而言是**全局**的数据，例如当前认证的用户、主题或首选语言。
```js
const MyContext = React.createContext(defaultValue);

```
创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。
只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。

***MyContext.Provider***
```js
<MyContext.Provider value={/* 某个值 */}>
```
每个 Context 对象都会返回一个 `Provider React` 组件，它允许消费组件订阅 context 的变化。

`Provider` 接收一个 `value` 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。

当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。

***Class.contextType***
```js
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}
MyClass.contextType = MyContext;
```
挂载在 class 上的 `contextType` 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象。这能让你使用 this.context 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。

***Context.Consumer***
```js
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>
```
这需要`函数作为子元素（function as a child）`这种做法。这个函数接收当前的 context 值，返回一个 React 节点。传递给函数的 value 值等同于往上组件树离这个 context 最近的 Provider 提供的 value 值。如果没有对应的 Provider，value 参数等同于传递给 createContext() 的 defaultValue。
