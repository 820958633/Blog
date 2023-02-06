promise

promise的理解

promise是异步编程的一种解决方案，他是一个对象，可以获取异步操作的消息，避免地狱回调。

Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说。Promise是一个对象，从它可以获取异步操作的消息。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。

promise的实例有三个状态：

- Pending（进行中）
- Resolved（已完成）
- Rejected（已拒绝）

当把一件事情交给promise时，它的状态就是Pending，任务完成状态就变成Resolved，没有完成失败了就变成了Rejected。

promise的实例有两个过程：

- Pending -> fulfilled : resolved （已完成）
- Pending -> rejected : rejected （已拒绝）

**注意：一旦从进行状态变成其他状态就永远不能更改状态了。**

promise的特点：

- 对象的状态不受外界影响。promise对象代表一个异步操作，有三种状态，pending（进行中）、fulfilled（已成功）、rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态，这也是promise这个名字的由来——“承诺”；
- 一旦状态改变就不会再变，任何时候都可以得到这个结果。promise对象的状态改变，只有两种可能：从pending变为fulfilled，从pending变为rejected。这时就称为resolved（已定型）。如果改变已经发生了，你再对promise对象添加回调函数，也会立即得到这个结果。这与事件（event）完全不同，事件的特点是：如果你错过了它，再去监听是得不到结果的。

promise的缺点：

- 无法取消promise，一旦新建它就会立即执行，无法中途取消。
- 如果不设置回调函数，promise内部抛出的错误，不会反应到外部。
- 当处于pending状态时，无法得知目前进展到那个一个阶段（刚开始还是即将完成）。

总结：

Promise 对象是异步编程的一种解决方案，最早由社区提出。Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。

状态的改变是通过 resolve() 和 reject() 函数来实现的，可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。

**注意：在构造promise的时候，构造函数内部的代码是立即执行的。**

promise的基本用法

1.创建promise对象

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。

```javascript
const promise = new Promise(function(resolve,reject){
  if(/*异步操作成功*/){
    resolve(value)
  }else{
    reject(error)
  }
}
```

**一般情况下都会使用new Promise() 来创建promise对象，但是也可以使用promise.resolve 和 promise.reject 这两个方法：**

- Promise.resolve

Promise.resolve(value) 的返回值也是一个promise对象，可以对返回值进行 .then调用：

```javascript
Promise.resolve(11).then(function(value){
  console.log(value)
})
```

resolve(11)代码中，会让promise对象进入确定(resolve状态)，并将参数11传递给后面的then所指定的onFulfilled 函数；

创建promise对象可以使用**new Promise**的形式创建对象，也可以使用**Promise.resolve(value)**的形式创建promise对象；

- Promise.reject

Promise.reject 也是 new promise 的快捷形式，也创建一个promise对象。

```javascript
Promise.reject(new Error("这是一个错误"))
// 相当于
new Promise(function(resolve,reject){
  reject(new Error("这是一个错误"))
})
```

例子：

```javascript
function testPromise(ready){
  return new Promise(function(resolve,reject){
    if(ready){
      console.log('hello world')
    }else{
      reject("No thanks")
    }
  })
}

testPromise(false).then(function(msg){
  console.log(msg) //hello world
},function(error){
  console.log(error) // No thanks
})
```

2.promise方法

**then( ) 、catch( )、 all( )、 race( )、 finally( )**

- 当promise执行的内容符合成功条件时，调用resolve函数，失败则调用reject函数。Promise创建完成后调用then()。

```javascript
Promise.then(function(value){
  // success
},function(error){
  // failure
})
```

then方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。其中第二个参数可以省略。
then方法返回的是一个新的Promise实例（不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。

当要写有顺序的异步事件时，需要串行时，可以这样写：

```javascript
let promise = new Promise((resolve,reject)=>{
    ajax('first').success(function(res){
        resolve(res);
    })
})
promise.then(res=>{
    return new Promise((resovle,reject)=>{
        ajax('second').success(function(res){
            resolve(res)
        })
    })
}).then(res=>{
    return new Promise((resovle,reject)=>{
        ajax('second').success(function(res){
            resolve(res)
        })
    })
}).then(res=>{
    
})
```

- Catch()

Promise对象除了有then方法，还有一个catch方法，该方法相当于then方法的第二个参数，指向reject的回调函数。不过catch方法还有一个作用，就是在执行resolve回调函数时，如果出现错误，抛出异常，不会停止运行，而是进入catch方法中。

```javascript
p.then((data) => {
     console.log('resolved',data);
},(err) => {
     console.log('rejected',err);
     }
); 
p.then((data) => {
    console.log('resolved',data);
}).catch((err) => {
    console.log('rejected',err);
});
```

- all 方法可以完成并行任务，他接受一个数组，数组的每一项都是一个 promise 对象。当数组中所有的 promise 的状态都达到了resolved 的时候， all 方法的状态就会变成 resolved ，如果有一个状态变成了 rejected，那么 all 方法的状态就会变成 rejected 。

```javascript
let promise1 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
       resolve(1);
	},2000)
});
let promise2 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
       resolve(2);
	},1000)
});
let promise3 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
       resolve(3);
	},3000)
});
Promise.all([promise1,promise2,promise3]).then(res=>{
    console.log(res); //结果为：[1,2,3] 
})
```

调用all 方法时结果成功返回的是回调函数的参数也是一个数组，这个数组按顺序保存着每一个promise对象resolve执行时的值。

- race 

race方法和all一样，接受的参数是一个每项都是promise的数组，但是与all不同的是，当最先执行完的事件执行完之后，就直接返回该promise对象的值。如果第一个promise对象状态变成resolved，那自身的状态变成了resolved；反之第一个promise变成rejected，那自身状态就会变成rejected。

当一件事超出多久就不做了可以用这个方法。

```javascript
let promise1 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
       reject(1);
	},2000)
});
let promise2 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
       resolve(2);
	},1000)
});
let promise3 = new Promise((resolve,reject)=>{
	setTimeout(()=>{
       resolve(3);
	},3000)
});
Promise.race([promise1,promise2,promise3]).then(res=>{
	console.log(res); //结果：2
},rej=>{
    console.log(rej)};
)
```

- Finally

finally( ) 方法用于无论promise对象结果如何都会执行的操作。

```javascript
server.listen(port)
  .then(function () {
    // ...
  })
  .finally(server.stop);  // 最终关闭服务器
```

finally方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是fulfilled还是rejected。这表明，finally方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。finally本质上是then方法的特例：

```javascript

promise
.finally(() => {
  // 语句
});
// 等同于
promise
.then(
  result => {
    // 语句
    return result;
  },
  error => {
    // 语句
    throw error;
  }
);
```

不用finally 就要写两次。
