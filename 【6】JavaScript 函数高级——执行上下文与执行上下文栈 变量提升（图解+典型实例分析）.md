

# JavaScript 函数高级——执行上下文与执行上下文栈、变量提升（图解+典型实例分析）

## 变量提升与函数提升

- 变量声明提升
  - 通过` var` 定义（声明）的变量，在定义语句之前就可以访问到
  - 值：`undefined`

```javascript
/*
  面试题 : 输出 undefined
   */
var a = 3
function fn() {
    console.log(a) // undefined  调用fn()后进入函数体内部，通过 var 声明的变量提升，值为undefined
    var a = 4
    console.log(a) // 4
}
fn()
console.log('----')
```

> 可以用开发工具的代码调试工具设置断点，清楚地看到`a`的值的变化。

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\执行上下文_变量声明提升.png)

- 函数声明提升
  - 通过 `function `声明的函数，在之前就可以直接调用
  - 值：函数定义（对象）

```javascript
console.log(b) // undefined  通过 var 声明的变量b         变量声明提升
fn2() // fn2()               通过 function 声明的函数fn2  函数声明提升
fn3() // 报错                 通过 var 声明的函数fn3       变量声明提升

var b = 3
function fn2() {
  console.log('fn2()')
}

var fn3 = function () {
  console.log('fn3()')
}
```

**！！注意 `var a = 3`， `var fn = function(){ }`只要是`var`声明的，都是变量声明提升,值都是`undefined`**

**！！注意：`var fn = function(){ }`属于变量声明提升，不是函数声明提升**

- 问题：变量提升和函数提升是如何产生的？

## 执行上下文

> 执行上下文个数 = n(调用的函数个数) + 1(全局执行上下文)

```javascript
// 变量声明提升，函数声明提升，全局代码的函数和方法添加为Window的方法和属性
console.log(a1, window.a1) // undefined   undefined
window.a2() // a2()
console.log(this) // Window

var a1 = 3
function a2() {
    console.log('a2()')
}
console.log(a1) // 3
```

### 代码分类（位置）

- 全局代码
- 函数代码

### 全局执行上下文

- 在**执行全局代码前**将`window`确定为全局执行上下文
- 对**全局**数据进行预处理：
  - `var `定义的全局变量==>`undefined`，添加为`window`属性
  - `function` 声明的全局函数==>赋值(`fun`)，添加为`window`的方法
  - `this`==>赋值(`window`)
- 开始执行全局代码

### 函数执行上下文

- 在**调用函数，准备执行函数体之前**，创建对应的函数执行上下文对象
- 对**局部**数据进行预处理
  - 形参变量==>赋值（实参）==>添加为执行上下文的属性
  - `arguments`==>赋值（实参列表），添加为执行上下文的属性
  - `var` 定义的局部变量==>`undefined`==>添加为执行上下文的属性
  - `function`声明的函数==>赋值（`fun`）==>添加为执行上下文的属性
  - `this`==>赋值（调用函数的对象）
- 开始执行函数体代码

## 执行上下文栈

### 理解

- 在全局代码执行前,` JS`引擎就会创建一个**栈**来存储管理所有的执行上下文对象

-  在**全局**执行上下文(`window`)确定后, 将其添加到栈中(压栈)

- 在**函数**执行上下文创建后, 将其添加到栈中(压栈)

- 在当前函数执行完后,将栈顶的对象移除(出栈)

-  当所有的代码执行完后, 栈中只剩下`window`

### 流程分析

```javascript
// 1. 进入全局上下文
var a = 10
var bar = function (x) {
    var b = 5
    // 3. 进入foo执行上下文
    foo(x + b)
}
var foo = function (y) {
    var c = 5
    console.log(a + c + y)
}
// 2. 进入bar函数执行上下文
bar(10)  // 30
```

- 用断点调试工具查看执行上下文调用栈

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\执行上下文_断点.png)

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\执行上下文_函数调用栈断点调试.png)

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\执行上下文_调用栈.png)

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\执行上下文_调用栈2.png)

## 面试题

### 题1：递归调用的执行上下文

```javascript
console.log('gb: ' + i)  
var i = 1
foo(1)
function foo(i) {
    if (i == 4) {
        return
    }
    console.log('fb:' + i)
    foo(i + 1) //递归调用: 在函数内部调用自己
    console.log('fe:' + i)
}
console.log('ge: ' + i) // ge:1
```

1. 依次输出什么?

   ![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\输出.png)

2. 整个过程中产生了几个执行上下文?  

   `5个 `= `4个函数执行上下文` + `1个全局执行上下文`

调试查看：

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\执行上下文_例1调试.png)

### 题2：函数提升优先于变量提升

```javascript
function a() { }
var a
console.log(typeof a) // 'function'
```

### 题3：考察全局环境下的变量提升

```javascript
if (!(t in window)) {
    var t = 1
}
console.log(t)  // undefined
```

```javascript
t in window
// true
```

原因就在于变量`t`被提升到了全局环境最顶部，通过关键字`var`在全局作用域中声明的变量将作为全局对象（window）的一个属性，上面的代码JavaScript其实是这样解析的：

```javascript
var t
if (!(t in window)) {
    var t = 1
}
console.log(t) // undefined
```

### 题4（好题）

```javascript
var c = 1
function c(c) {
  console.log(c)
  var c = 3
}
c(2) // 报错  c is not a function 
```

注意：函数提升为`c()`，整个函数提升，所以上面的代码相当于下面这样，这样就很清楚了吧！

```javascript
function c(c) {
    console.log(c)
    var c = 3
}
var c

c = 1
c(2) //所以 c is not a function
```

函数体内的代码根本就没机会执行。