# `JavaScript`线程机制与事件机制

## 一、进程与线程

### 进程（`process`）

- 程序的一次执行，它占有一片独有的内存空间。
- 可以通过`windows`任务管理器查看进程。

### 线程（`thread`）

- 是进程内的一个独立执行单元。
- 是程序执行的一个完整流程。
- 是`CPU`的最小调度单元。

### 进程与线程图解

![](.\media\进程与线程图解.png)

### 相关知识

- 应用程序必须运行在某个进程的某个线程上。
- 一个进程中至少有一个运行的线程：主线程，进程启动后自动创建。
- 一个进程中也可以同时运行多个线程，我们会说程序是多线程运行的。
- 一个进程内的数据可以供其中的多个线程中直接共享。
- 多个进程之间的数据是不能直接共享的。
- 线程池（`thread pool`）：保存多个线程对象的容器，实现线程对象的反复利用。

### 相关问题

（1）何为多进程与多线程？

- 多进程运行：一个应用程序可以同时启动多个实例运行。
- 多线程：在一个进程内，同时有多个线程运行。

（2）比较单线程与多线程？

- 多线程
  - 优点：能有效提升`CPU`的利用率。
  - 缺点：
    - 创建多线程开销。
    - 线程间切换开销。
    - 死锁与状态同步问题。

- 单线程
  - 优点：顺序编程简单易懂。
  - 缺点：效率低。

（3）`JS`是单线程还是多线程？

- `JS`是单线程运行的。
- 但是使用`H5`中的 `Web Workers`可以多线程运行。

（4）浏览器运行是单线程还是多线程？

- 浏览器都是多线程运行的。

（5）浏览器运行是单进程还是多进程？

- 有的是单进程：
  - 老版`Firefox`
  - 老版`IE`
- 有的是多进程：
  - `Chrome`
  - 新版`Firefox`
  - 新版`IE`
- 如何查看浏览器是否是多进程运行的呢？
  - 任务管理器==>进程

## 二、浏览器内核

（1）浏览器内核是支撑浏览器运行的最核心的程序。

（2）不同的浏览器内核不一样：

- `Chrome`，`Safari`：`webkit`
- `Firefox`：`Gecko`
- `IE`：`Trident`
- `360`，搜狗等国内浏览器：`Trident+webkit`

（3）内核由很多模块组成：

- 主线程
  - `js`引擎模块：负责`js`程序的编译与运行。
  - `html`,`css`文档解析模块：负责页面文本的解析。
  - `DOM/CSS`模块：负责`DOM/CSS`在内存中的相关处理。
  - 布局和渲染模块：负责页面的布局和效果的绘制（内存中的对象）
- 分线程
  - 定时器模块：负责定时器的管理。
  - `DOM`事件响应模块：负责事件的管理。
  - 网络请求模块：负责服务器请求（常规/`ajax`）。

## 三、定时器引发的思考

（1）定时器真是定时执行的吗?

- 定时器并不能保证真正定时执行。

- 一般会延迟一丁点(可以接受), 也有可能延迟很长时间(不能接受)。

```javascript
<button id="btn">启动定时器</button>

document.getElementById('btn').onclick = function () {
  var start = Date.now()
  console.log('启动定时器前...')
  setTimeout(function () {
    console.log('定时器执行了', Date.now()-start)
  }, 200)
  console.log('启动定时器后...')
}
```

![1561622459826](.\media\定时器1.png)

给上面回调函数加一个长时间的任务：

```javascript
document.getElementById('btn').onclick = function () {
  var start = Date.now()
  console.log('启动定时器前...')
  setTimeout(function () {
    console.log('定时器执行了', Date.now()-start)
  }, 200)
  console.log('启动定时器后...')
  // 做一个长时间的工作
  for (var i = 0; i < 1000000000; i++) {}
}
```

结果：

![1561622616205](.\media\定时器2.png)

同步任务执行完之后才会执行异步任务。

（2）定时器回调函数是在分线程执行的吗?

- 在主线程执行的, `js`是单线程的。

（3）定时器是如何实现的?

- 事件循环模型(后面讲)。

## 四、`JS`是单线程执行的

（1）如何证明`js`执行是单线程的?

- `setTimeout()`的回调函数是在主线程执行的。

- 定时器回调函数只有在运行栈中的代码全部执行完后才有可能执行。

（2）为什么`js`要用单线程模式, 而不用多线程模式?

- `JavaScript`的单线程，与它的用途有关。

- 作为浏览器脚本语言，`JavaScript`的主要用途是与用户互动，以及操作`DOM`。

- 这决定了它只能是单线程，否则会带来很复杂的同步问题

（3）代码的分类:

- 初始化代码

-  回调代码

（4）`js`引擎执行代码的基本流程

- 先执行初始化代码: 包含一些特别的代码 ，回调函数(异步执行)
  - 设置定时器
  - 绑定监听
  - 发送`ajax`请求

- 后面在某个时刻才会执行回调代码。

## 五、浏览器的事件循环（轮询）模型

### 浏览器的事件循环模型原理图

![1561620087294](.\media\事件循环模型原理图.png)

### 相关重要概念

（1）执行栈

- `execution stack`
- **所有的**代码都是在此空间中执行的。

（2）浏览器内核

- `browser core`

- `js`引擎模块(在主线程处理)

- 其它模块(在主/分线程处理)

（3）任务队列(`callback queue`)

​     `task queue`

（4）消息队列(`callback queue`)

​     `message queue`

（5）事件队列(`callback queue`)

​    ` event queue`

（6）事件轮询

- `event loop`
- 从任务队列中循环取出回调函数放入执行栈中处理(一个接一个)。

（7）事件驱动模型

​     `event-driven interaction model`

（8）请求响应模型

​    ` request-response model`

### 执行流程

（1）所有代码分类：

- 初始化执行代码：包含绑定`dom`事件监听, 设置定时器, 发送`ajax`请求的代码。

- 回调执行代码：处理回调逻辑。

（2）`js`引擎执行代码的基本流程：

- 初始化代码===>回调代码

（3）模型的2个重要组成部分：

- 事件管理模块

- 回调队列

（4）**模型的运转流程**：

​	（a）执行初始化代码, 将事件回调函数交给对应模块管理。

​	（b）当事件发生时, 管理模块会将回调函数及其数据添加到回调列队中。

​	（c）只有当初始化代码执行完后(可能要一定时间), 才会遍历读取回调队列中的回调函数执行。

## 六、`H5 Web Workers`（多线程）

### 介绍

- `Web Workers` 是 `HTML5` 提供的一个`Javascript`**多线程**解决方案。
- 我们可以将一些大计算量的代码交由`Web Worker`运行而不冻结用户界面。
- 但是子线程完全受主线程控制，且不得操作`DOM`。所以，这个新标准并没有改变`JavaScript`单线程的本质

### 使用

- 相关`API`
  - `Worker`: 构造函数, 加载分线程执行的`js`文件。
  - `Worker.prototype.onmessage`: 用于接收另一个线程的回调函数。

  - `Worker.prototype.postMessage`: 向另一个线程发送消息。

- 创建在分线程执行的`JS`文件

```javascript
var onmessage = function (event){ //不能用函数声明
    console.log('onMessage()22');
    var upper = event.data.toUpperCase();//通过event.data获得发送来的数据
    postMessage( upper );//将获取到的数据发送回主线程
}
```

- 在主线程中的`JS`中发消息并设置回调

```javascript
//创建一个Worker对象并向它传递将在新线程中执行的脚本的URL
var worker = new Worker("worker.js");  
//接收worker传过来的数据函数
worker.onmessage = function (event) {     
    console.log(event.data);             
};
//向worker发送数据
worker.postMessage("hello world");    
```

### 图解

![1561621147732](.\media\webworkers.png)

### 应用练习

```javascript
编程实现斐波那契数列（Fibonacci sequence）的计算

F（0）=0，F（1）=1，..... F（n）=F(n-1)+F(n-2)
```

- 直接在主线程：

```javascript
var fibonacci =function(n) {
    return n <2 ? n : fibonacci(n -1) + fibonacci(n -2);
};
console.log(fibonacci(48));
```

- 使用`Web Workers`在分线程：
  - 主线程：

  ```javascript
  var worker = new Worker('worker2.js');
  worker.addEventListener('message', function (event) {
      var timer2 = new Date().getTime();
      console.log('结果：' + event.data, '时间:' + timer2, '用时：' + ( timer2 - timer ));
  }, false);
  var timer = new Date().getTime();
  console.log('开始计算: ', '时间:' + timer);
  setTimeout(function () {
      console.log('定时器函数在计算数列时执行了', '时间:' + new Date().getTime());
  }, 1000);
  worker.postMessage(40);
  console.log('我在计算数列的时候执行了', '时间:' + new Date().getTime());
  ```

  - 分线程：

  ```javascript
  var fibonacci =function(n) {
      return n <2 ? n : fibonacci(n -1) + fibonacci(n -2);
  };
  var onmessage = function(event) {
      var n = parseInt(event.data, 10);
      postMessage(fibonacci(n));
  };
  ```

### 不足

- 慢。

- 不能跨域加载`JS`。

- `worker`内代码不能访问`DOM`(更新`UI`)。

- 不是每个浏览器都支持这个新特性。