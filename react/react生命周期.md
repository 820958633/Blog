# React 的生命周期有哪些

## React 通常将组件生命周期分成三个阶段：

- 装载阶段（Mount），组件第一次在 DOM 树中被渲染的过程；
- 更新阶段（Update），组件状态发生变化，重新更新渲染的过程；
- 卸载阶段（Unmount），组件从 DOM 树中被移出的过程；

![image-20230209163225533](/Users/zhaozhaoyang/Library/Application Support/typora-user-images/image-20230209163225533.png)

### 1.组件挂载阶段：

挂载阶段组件被创建，然后组价实例插入到 DOM 中，完成组件的第一次渲染，该过程只会发生一次，在此阶段会依次调用以下方法：

- constructor
- getDerivedStateFromProps
- render
- componentDidMount

### 2.组件更新阶段：

当组件的 props 发生变化时，或者内部调用了 setState/forceUpdate，会触发重新渲染，这个过程可能会发生多次。这个阶段会依次调用以下方法：

- getDerivedStateFromProps
- shouldComponentUpdate
- render
- getSnapshotBeforeUpdate
- componentDidUpdate

### 3.卸载阶段：

- componentWillUnmount

卸载阶段只有一个生命周期函数，componentWillUnmount() 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作：

- 清除 timer，取消网络请求或清除。
- 取消在 componentDidMount() 中创建的订阅等；

这个生命周期在一个组件被卸载个销毁之前被调用，因此不应该在这个方法中使用 setState，因为组件一旦被卸载，就不会再装载，也就不会重新渲染。

### 4.错误处理阶段

- componentDidCatch(error,info)，此生命周期在后代组件抛出错误后被调用。他接受两个参数：

1. error : 抛出的错误。
2. info : 带有componentStack key 的对象，其中包含有关组件引发错误的栈信息。

### React常见生命周期的过程大致如下：

- 挂载阶段，首先执行 constructor 构造方法来创建组件。
- 创建完成后，执行 render 方法，该方法会返回需要渲染的内容。
- 将需要渲染的内容挂载到 DOM 树上。
- 挂载完成之后执行 componentDidMount 生命周期函数。
- 如果我们给组件创建一个props（用于组件通信）、调用 setState （更改state中的数据）、调用 forceUpdate（强制更新组件）时，都会重新调用 render 函数。
- render 函数重新执行后，就会重新进行 DOM 树的挂载。
- 挂载完成之后就会执行 componentDidUpdate 生命周期函数。
- 当移出组件后，就会执行 componentWillUnmount 函数。

### React 主要生命周期总结：

- getDefaultProps : 这个函数会在组件创建之前被调用一次（有且仅有一次），它用来初始化组件的 Props；
- getInitalState : 用于初始化组件的 state 值；
- ComponentWillMount : 在组件创建后，render 之前，会走到 componentWillMount 阶段。这个阶段非常鸡肋。React   官方也不推荐使用。**React16废弃了这个生命周期。**
- render :  这是所有生命周期中唯一一个必须要实现的方法。一般来说需要返回一个 jsx 元素，这时 React 会根据 props 和 state 来把组件渲染到界面上；不过有时不想渲染任何东西可以返回null 或 false即可;
- componentDidMount : 会在组件挂载后（插入DOM）立即调用，标志着组件挂载完成。一些操作如果依赖获取到DOM节点信息，我们就会放在这个阶段来做。此外，这还是 React 官方推荐的发起 ajax 请求的时机。该方法和 componentWillMount 一样，有且仅有一次调用。
