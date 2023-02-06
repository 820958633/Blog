## This/call/apply/bind

## 1.对this对象的理解

this是执行上下文中的一个属性，他指向最后一次调用这个方法的对象。在实际开发中，this的指向可以通过四种调用模式来判断。

1. **函数调用模式，**当一个函数不是一个对象的属性时，直接作为函数来调用时，this指向全局对象。
2. **方法调用模式，**入股一个函数作为一个对象的方法来调用时，this指向这个对象。
3. **构造器调用模式，**如果一个函数用new调用时，函数执行前会创建一个新的对象，this指向这个新创建的对象。
4. **apply、call、bind，**这三种方式都可以显式的指定调用函数的this指向。

- apply方法接受两个参数：一个是this绑定的对象，一个是参数数组。
- call方法接收的参数，第一个是this绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用call()方法时，传递给函数的参数必须逐个列举出来。
- bind方法通过传入一个对象，返回一个this绑定了传入对象的新函数。这个函数的this指向除了使用new是会改变，其他情况下都不会改变。

这四种方式，使用构造器调用模式的优先级最高，然后是apply、call和bind调用模式，然后是方法调用模式，然后是函数调用模式。

## 2.call()和apply()的区别

他们的作用一模一样，区别仅在于传入参数的形式不同。

- apply接受两个参数，第一个参数指定了函数体内this对象的指向，第二个参数作为一个带下标的集合，这个集合可以为数组也可以为类数组，apply方法把这个集合中的元素作为参数传递给被调用的函数。
- call传入的参数数量不固定，跟apply相同的是，第一个参数也是代表函数体内的this指向，从第二个参数开始往后，每个参数被依次传入函数。

## 3.实现call、apply、和bind函数

（1）call函数的实现步骤：

- 判断调用对象是否为函数，即使是定义在函数的原型上的，但是也有可能出现使用call等方式调用的情况。（意思是call、apply、bind虽然定义在函数的原型上，也就是一般情况下只有函数类型才可以使用call，apply，bind，但是别的数据类型也可以通过call的方式来调用这三个方法，所以要判断一下调用对象是否为函数）
- 判断传入下下文对象是否存在，如果不存在，则设置为window。
- 处理传入的参数，截取第一个参数后的所有参数。
- 将函数作为上下文对象的一个属性。
- 使用上下文对象来调用这个方法，并保存返回结果。
- 删除刚才新增的属性。
- 返回结果。

```javascript
Function.prototype.myCall = function (context) {
  // 判断调用对象是否为函数
  if (typeof this !== "function") {
    console.error("type error");
  }
  // 获取参数
  let args = [...arguments].slice(1);
  let result = null;
  // 判断context是否传入，如果未传入则设置为 window
  context = context || window;
  // 将调用函数设为对象的方法
  context.fn = this;
  // 调用函数
  result = context.fn(...args);
  // 将属性删除
  delete context.fn;
  return result;
};
```

（2）apply函数的实现：

- 判断调用对象是否为函数。
- 判断传入上下文对象是否存在，如果不存在，则设置为window。
- 将函数作为上下文对象的一个属性。
- 判断参数值是否传入。
- 使用上下文对象来调用这个方法，并保存返回结果。
- 删除刚才新增的属性。
- 返回结果。

```javascript
// 与call的区别在于：第一个参数为this,第二个参数为数组
Function.prototype.myApply = function (context) {
  if(typeof context !== "function"){
    console.error("type error");
  }
  let result = null;
  context = context || window;
  context.fn = this;
  if(arguments[1]){
    result = context.fn(...arguments[1]);
  }else{
    result = context.fn();
  }
  delete context.fn;
  return result;
}
```

(3)bind函数的实现：

- 判断调用对象是否为函数。

- 保存当前函数的引用，获取其余传入参数值。

- 创建一个函数返回。

- 函数内部使用apply来绑定函数调用，需要判断函数作为构造函数的情况，这个时候需要传入当前函数的this给apply调用，其余情况都传入指定的上下文对象。

  ```javascript
  // bind 会返回一个新函数，传参与call相同
  Function.prototype.myBind = function (context) {
    if(typeof context !== "function"){
      console.error("type error");
    }
    // 获取参数
    var args = [...arguments].slice(1);
    var fn = this;
    // 根据调用方式，传入不同的绑定值
    return function Fn() {
      return fn.apply(this instanceof Fn ? this : context, args.concat(...arguments))
    }
  }
  ```

  为什么要合并参数args.concat(...arguments))

  args是绑定上下文的时候（也就是bind()）传递的参数，而后面的arguments则是调用bind()()第二个括号传递的参数，所以这里才会需要合并。

  ```javascript
  //my_bind方法不仅可以绑定对象，还可以传参
  Function.prototype.my_bind = function(context){
  	var args = Array.prototype.slice.call(arguments, 1);
  	//args [7, 8]
  	var self = this;
  	return function(){
  		var innerArgs = Array.prototype.slice.call(arguments);
  		//innerArgs [9]
  		var finalArgs = args.concat(innerArgs);
  		//finalArgs [7, 8, 9]
  		return self.apply(context, finalArgs);
  	}
  }
   
  //测试
  function a(m, n, o){
  	return this.name + ' ' + m + ' ' + n + ' ' + o;
  }
  var b = {name : 'kong'};
  a.my_bind(b, 7, 8)(9);		//kong 7 8 9
  
  ```

  

