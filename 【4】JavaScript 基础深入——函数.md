# JavaScript 基础深入——函数

## 函数基础

### 什么是函数？

- 实现特定功能的 n 条语句的封装体
- 只有函数是可以执行的，其它类型的数据不能执行

### 为什么要用函数？

- 提高代码复用
- 便于阅读交流

### 如何定义函数？

- 函数声明
- 表达式

```javascript
function fn1 () { //函数声明
  console.log('fn1()')
}
var fn2 = function () { //表达式
  console.log('fn2()')
}
```

### 如何调用（执行）函数？

- `test()`：直接调用
- `obj.test()`：通过对象调用
- `new test()`：new 调用
- `test.call/apply(obj)`：临时让`test`成为`obj`的方法进行调用

```javascript
var obj = {}
function test2 () {
  this.xxx = 'atguigu'
}
// obj.test2()  不能直接, 根本就没有
test2.call(obj) // obj.test2()   // 可以让一个函数成为指定任意对象的方法进行调用
console.log(obj.xxx) // 'atguigu
```

## 回调函数

### 什么函数才是回调函数？

- 你定义的
- 你没有调用
- 但最终它执行了（在某个时刻或某个条件下）

### 常见的回调函数

- dom 事件回调函数==> 发生事件的dom元素
- 定时器回调函数 ===> window

- ajax 请求回调函数
- 生命周期回调函数

```javascript
document.getElementById('btn').onclick = function () { // dom事件回调函数
  alert(this.innerHTML)
}
//定时器
  // 超时定时器
  // 循环定时器
setTimeout(function () { // 定时器回调函数
  alert('到点了'+this)
}, 2000)
```

## `IIFE`(立即执行函数)

### 理解

- 全称：`Immediately-Invoked Function Expression`

### 作用

- 隐藏实现
- 不会污染外部（全局）命名空间
- 用它来编码js模块

```javascript
;(function () { //匿名函数自调用
  var a = 3
  console.log(a + 3)
})()

var a = 4
console.log(a)

;(function () {
  var a = 1
  function test () {
    console.log(++a)
  }
  window.$ = function () { // 向外暴露一个全局函数
    return {
      test: test
    }
  }
})()

$().test() // 1. $是一个函数 2. $执行后返回的是一个对象
```

## 函数中的`this`

### `this`是什么？

- 任何函数本质上都是通过某个对象来调用的,如果没有直接指定就是window
- 所有函数内部都有一个变量`this`
- 它的值是调用函数的当前对象

### 如何确定`this`的值？

- `test()`: `window`

- `p.test()`: `p`

-  `new test()`: 新创建的对象

- `p.call(obj)`: `obj`

```javascript
function Person(color) {
  console.log(this)
  this.color = color;
  this.getColor = function () {
    console.log(this)
    return this.color;
  };
  this.setColor = function (color) {
    console.log(this)
    this.color = color;
  };
}

Person("red"); //this是谁? window

var p = new Person("yello"); //this是谁? p

p.getColor(); //this是谁? p

var obj = {};
p.setColor.call(obj, "black"); //this是谁? obj

var test = p.setColor;
test(); //this是谁? window

function fun1() {
  function fun2() {
    console.log(this);
  }
  fun2(); //this是谁? window
}
fun1();
```

