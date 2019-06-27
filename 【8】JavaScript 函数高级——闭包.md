# `JavaScript`函数高级——闭包

## 一、引子实例

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>00_引入</title>
</head>
<body>
	<button>测试1</button>
	<button>测试2</button>
	<button>测试3</button>
<!--
需求: 点击某个按钮, 提示"点击的是第n个按钮"
-->
<script type="text/javascript">
  var btns = document.getElementsByTagName('button')
  //遍历加监听
  /*
  for (var i = 0,length=btns.length; i < length; i++) {
    var btn = btns[i]
    btn.onclick = function () {
      alert('第'+(i+1)+'个')
    }
  }*/
  /*
  for (var i = 0,length=btns.length; i < length; i++) {
    var btn = btns[i]
    //将btn所对应的下标保存在btn上
    btn.index = i
    btn.onclick = function () {
      alert('第'+(this.index+1)+'个')
    }
  }*/

  //利用闭包
  for (var i = 0,length=btns.length; i < length; i++) {
    (function (j) {
      var btn = btns[j]
      btn.onclick = function () {
        alert('第'+(j+1)+'个')
      }
    })(i)
  }
</script>
</body>
</html>
```

## 二、理解闭包

（1）如何产生闭包?

- 当一个嵌套的**内部(子)函数**引用了嵌套的**外部(父)函数**的变量(函数)时, 就产生了闭包。

（2）闭包到底是什么?

- 使用`chrome`调试查看。

- 理解一: 闭包是嵌套的内部函数(绝大部分人)。

- 理解二: 包含被引用变量(函数)的对象(极少数人)。

- 注意: 闭包存在于嵌套的内部函数中。

（3）产生闭包的条件?

- 函数嵌套

- 内部函数引用了外部函数的数据(变量/函数)

## 三、常见的闭包

（1）将函数作为另一个函数的返回值

```javascript
// 1. 将函数作为另一个函数的返回值
function fn1() {
  var a = 2
  function fn2() {
    a++
    console.log(a)
  }
  return fn2
}
var f = fn1()
f() // 3
f() // 4
```

（2）将函数作为实参传递给另一个函数调用

```javascript
// 2. 将函数作为实参传递给另一个函数调用
function showDelay(msg, time) {
  setTimeout(function () {
    alert(msg)
  }, time)
}
showDelay('atguigu', 2000)
```

## 四、闭包的作用

（1）使用函数内部的变量在函数执行完后, 仍然存活在内存中(延长了局部变量的生命周期)

（2）让函数外部可以操作(读写)到函数内部的数据(变量/函数)

```javascript
function fn1() {
  var a = 2
  function fn2() {
    a++
    console.log(a)
    // return a
  }
  function fn3() {
    a--
    console.log(a)
  }-
  return fn3
}

var f = fn1()
f() // 1
f() // 0
```

问题:

- 函数执行完后, 函数内部声明的局部变量是否还存在?

 一般是不存在, 存在于闭包中的变量才可能存在。

- 在函数外部能直接访问函数内部的局部变量吗?

不能, 但我们可以通过闭包让外部操作它。

## 五、闭包的生命周期

（1）产生: 在嵌套**内部函数定义**执行完时就产生了(不是在调用时)。

（2）死亡: 在嵌套的**内部函数**成为垃圾对象时。

```javascript
function fn1() {
  //此时闭包就已经产生了(函数提升, 内部函数对象已经创建了)
  var a = 2
  function fn2 () {
    a++
    console.log(a)
  }
  return fn2
}
var f = fn1()
f() // 3
f() // 4
f = null //闭包死亡(包含闭包的函数对象成为垃圾对象)
```

## 六、闭包的应用：自定义JS模块

闭包的应用 : 定义`JS`模块

- 具有特定功能的`js`文件。
- 将所有的数据和功能都封装在一个函数内部(私有的)。
- 只向外暴露一个包含`n`个方法的对象或函数。
- 模块的使用者, 只需要通过模块暴露的对象调用方法来实现对应的功能。

第一种方式：

```javascript
// myModule.js
function myModule() {
  //私有数据
  var msg = 'My data'
  //操作数据的函数
  function doSomething() {
    console.log('doSomething() '+msg.toUpperCase())
  }
  function doOtherthing () {
    console.log('doOtherthing() '+msg.toLowerCase())
  }

  //向外暴露对象(给外部使用的方法)
  return {
    doSomething: doSomething,
    doOtherthing: doOtherthing
  }
}
```

```javascript
// 使用模块：
<script type="text/javascript" src="myModule.js"></script>
<script type="text/javascript">
  var module = myModule()
  module.doSomething()
  module.doOtherthing()
</script>
```

第二种方式：

```javascript
// myModule2.js
(function () {
  //私有数据
  var msg = 'My atguigu'
  //操作数据的函数
  function doSomething() {
    console.log('doSomething() '+msg.toUpperCase())
  }
  function doOtherthing () {
    console.log('doOtherthing() '+msg.toLowerCase())
  }

  //向外暴露对象(给外部使用的方法)
  window.myModule2 = {
    doSomething: doSomething,
    doOtherthing: doOtherthing
  }
})()
```

```javascript
// 使用模块
<script type="text/javascript" src="myModule2.js"></script>
<script type="text/javascript">
  myModule2.doSomething()
  myModule2.doOtherthing()
</script>
```

## 七、闭包的缺点及解决

（1）缺点

- 函数执行完后, 函数内的局部变量没有释放, 占用内存时间会变长。

- 容易造成内存泄露。

（2）解决

- 能不用闭包就不用。

- 及时释放。

```javascript
function fn1() {
  var arr = new Array(10000000)
  function fn2() {
    console.log(arr.length)
  }
  return fn2
}
var f = fn1()
f()

f = null //让内部函数成为垃圾对象-->回收闭包
```

## 八、闭包_面试题

面试题1：

```javascript
//代码片段一
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        return function(){
            return this.name;
        };
    }
};
alert(object.getNameFunc()());  // The Window

//代码片段二
var name2 = "The Window";
var object2 = {
    name2 : "My Object",
    getNameFunc : function(){
        var that = this;
        return function(){
            return that.name2;
        };
    }
};
alert(object2.getNameFunc()()); // My Object
```

面试题2：

```javascript
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n)
    }
  }
}
var a = fun(0)
a.fun(1)
a.fun(2)
a.fun(3)//undefined,0,0,0

var b = fun(0).fun(1).fun(2).fun(3)//undefined,0,1,2

var c = fun(0).fun(1)
c.fun(2)
c.fun(3)//undefined,0,1,1
```

可通过断点调试查看执行步骤。