作用域个数 = n(定义的函数个数) + 1(全局作用域)

# JavaScript函数高级——作用域与作用域链

## 一、作用域

（1）理解

- 就是一块"地盘", 一个代码段所在的区域。

- 它是静态的(相对于上下文对象), 在编写代码时就确定了。

（2）分类

- 全局作用域

- 函数作用域

- `ES6`中新增了块级作用域

（3）作用

- 隔离变量，不同作用域下同名变量不会有冲突。

![](.\media\作用域图.png)

## 二、作用域与执行上下文

（1）区别1

- 全局作用域之外，每个函数都会创建自己的作用域，作用域在函数定义时就已经确定了。而不是在函数调用时。

- 全局执行上下文环境是在全局作用域确定之后, `js`代码马上执行之前创建。

- 函数执行上下文环境是在调用函数时, 函数体代码执行之前创建。

（2）区别2

- 作用域是静态的, 只要函数定义好了就一直存在, 且不会再变化。

- 上下文环境是动态的, 调用函数时创建, 函数调用结束时上下文环境就会被释放。

（3）联系

- 上下文环境(对象)是从属于所在的作用域。

- 全局上下文环境==>全局作用域

- 函数上下文环境==>对应的函数作用域

![](.\media\作用域与执行上下文.png)

## 三、作用域链

（1）理解

- 多个上下级关系的作用域形成的链, 它的方向是从下向上的(**从内到外**)

- 查找变量时就是沿着作用域链来查找的。

（2）查找一个变量的查找规则

- 1）在当前作用域下的执行上下文中查找对应的属性, 如果有直接返回, 否则进入2）。

- 2）在上一级作用域的执行上下文中查找对应的属性, 如果有直接返回, 否则进入3）。

- 3）再次执行2）的相同操作, 直到全局作用域, 如果还找不到就抛出找不到的异常。

```javascript
var a = 2;
function fn1() {
    var b = 3;
    function fn2() {
        var c = 4;
        console.log(c);
        console.log(b);
        console.log(a);
        console.log(d);
    }
    fn2();
}
fn1();
```

![](.\media\作用域链.png)

## 四、作用域_面试题

面试题1

```javascript
var x = 10;
function fn() {
  console.log(x);
}
function show(f) {
  var x = 20;
  f();
}
show(fn); // 结果 10
// 由于fn()的作用域中没有找到属性x,则会去fn()的上一级作用域也就是全局作用域中找，找到x=10，因此打印10.
```

面试题2

```javascript
var fn = function () {
  console.log(fn)
}
fn()

var obj = {
  fn2: function () {
    console.log(fn2)
  }
}
obj.fn2()
```

![1561625753231](.\media\作用域题目2-1.png)

上面第一个会正常打印出`fn()`

第二个，调用时是`obj.fn2()`，而`obj.fn2() = function(){console.log(fn2)}`

所以相当于`window`调用`function(){console.log(window.fn2)}`，而`window`中没有`fn2`属性，所以会报错。

将代码改变：

```javascript
var fn = function () {
  console.log(fn)
}
fn()

var obj = {
  fn2: function () {
    // console.log(fn2)
    console.log(this.fn2)
  }
}
obj.fn2()
```

![1561626088717](.\media\作用域题目2-2.png)

`obj.fn2()`调用时`this`的指向为`obj`，所以相当于`function(){console.log(obj.fn2)}`，`obj`中有`fn2`属性，所以能成功执行。