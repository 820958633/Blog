# React 组件通信

## React 组件间通信常见的几种情况：

- 父组件向子组件通信
- 子组件向父组件通信
- 跨级组件通信
- 非嵌套关系的组件通信

### 1.父子组件的通信方式

- 父组件向子组件通信：父组件通过props向子组件传递需要的信息。
- 子组件向父组件通信：props + 回调的方式（父组件传过来一个方法）。

### 2.跨级组件的通信

父组件向子组件的子组件通信，即向更深的子组件通信：

- 使用props，利用中间组件层层传递,但是如果父组件结构较深，那么中间每一层组件都要去传递props，增加了复杂度，并且这些props并不是中间组件自己需要的。
- 使用context，context相当于一个大容器，可以把要通信的内容放在这个容器中，这样不管嵌套多深，都可以随意取用，对于跨越多层的全局数据可以使用context实现。

```javascript
// context方式实现跨级组件通信 
// Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据
```

首先创建一个context.js文件（与父子孙同个目录），默认值为一个对象。

```javascript
import React from "react";
const MyContext = React.createContext({text:'luck'});
export default MyContext
```

对父组件进行改写，引入context，使用一个 Provider 来将当前的 value 传递给以下的组件树，value为传递的值。

```javascript
import React from 'react';
import Children from './Children';
import MyContext from './context';
 
class Parent extends React.Component {
  constructor(props) {
	super(props);
  }
  // 使用一个 Provider 来将当前的 value 传递给以下的组件树。
  // 无论多深，任何组件都能读取这个值。
  render(){
    return (
      <div style={{backgroundColor:'#f7ba2a',padding:'20px',width:'500px',margin:'auto',textAlign:'center'}}>
        <p>context通信实例</p>
        <MyContext.Provider value={{text:'good luck'}}>
          <Children></Children>
        </MyContext.Provider>
      </div>
    )
  }
}
 
export default Parent
```

子组件为中间层，不做处理，用于包裹“孙”组件。

```javascript
import React from 'react';
import Grandson from './Grandson';
 
class Children extends React.Component {
  render(){
    return (
      <div>
        <Grandson></Grandson>
      </div>
    )
  }
}
 
export default Childre
```

新增一个“孙”组件，同样需引入context，在组件内部添加**static contextType = MyContext**，此时将能通过this.context直接获取到上层距离最近的Provider传递的值，此时this.context = {text:good luck}，即父组件传递value。

```javascript
import React from 'react';
import MyContext from './context';
 
class Grandson extends React.Component {
  static contextType = MyContext
  render(){
    return (
      <div style={{backgroundColor:'#13ce66',padding:'10px',width:'200px',margin:'auto',marginTop:'20px'}}>
        <p>通过context传过来：</p>
				//通过this.context.text获取到传递的值。
        <span style={{color:'blue'}}>{this.context.text}</span>
      </div>
    )
  }
}
 
export default Grandson
```

以上的是一个父-->孙的过程，即向下的流程，如果想孙-->父向上传值，可以通过回调的方式。

### 3.非嵌套关系组件的通信方式

没有包含任何关系的组件，包括兄弟组件以及不在同一个父级的非兄弟组件。

- 可以使用自定义事件通信（发布订阅模式）
- 可以通过redux等进行全局状态管理
- 如果是兄弟组件通信，可以找到这两个兄弟节点共同的父节点, 结合父子间通信方式进行通信。

### 4.如何解决 props 层级过深的问题

- 使用Context API：提供一种组件之间的状态共享，而不必通过显式组件树逐层传递props；
- 使用Redux等状态库。

### 5.组件通信的方式有哪些

- **⽗组件向⼦组件通讯**: ⽗组件可以向⼦组件通过传 props 的⽅式，向⼦组件进⾏通讯。
- **⼦组件向⽗组件通讯**: props+回调的⽅式，⽗组件向⼦组件传递props进⾏通讯，此props为作⽤域为⽗组件⾃身的函 数，⼦组件调⽤该函数，将⼦组件想要传递的信息，作为参数，传递到⽗组件的作⽤域中。
- **兄弟组件通信**: 找到这两个兄弟节点共同的⽗节点,结合上⾯两种⽅式由⽗节点转发信息进⾏通信 。
- **跨层级通信**: Context 设计⽬的是为了共享那些对于⼀个组件树⽽⾔是“全局”的数据，例如当前认证的⽤户、主题或⾸选语⾔，对于跨越多层的全局数据通过 Context 通信再适合不过 。
- **发布订阅模式**: 发布者发布事件，订阅者监听事件并做出反应,我们可以通过引⼊event模块进⾏通信 。
- **全局状态管理⼯具**: 借助Redux或者Mobx等全局状态管理⼯具进⾏通信,这种⼯具会维护⼀个全局状态中⼼Store,并根据不同的事件产⽣新的状态。

