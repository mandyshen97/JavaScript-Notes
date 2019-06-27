# 原型与原型链深入理解（图解）

## 原型（`prototype`）

函数的` prototype` 属性（图）

- 每个函数都有一个`prototype`属性，它默认指向一个`Object`空对象（即称为：原型对象）
- 原型对象中有一个属性`constructor`，它指向函数对象

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_原型.png)

给原型对象添加属性（一般都是添加方法）

- 作用：函数的所有实例对象自动拥有圆形中的属性（方法）

## 显式原型与隐式原型

- 每个函数` function` 都有一个`prototype`属性，即 显式原型
- 每个实例对象都有一个`__proto__`，可称为隐式原型
- 实例对象的隐式原型的值` === `其对应构造函数的显示原型的值
- 内存结构（图）

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_内存结构.png)

- 总结：
  - 函数的 `prototype` 属性：在定义函数时自动添加的，默认值是一个空的`Object`对象
  - 对象的`__proto__`属性：创建对象时自动添加的，默认值为构造函数的`prototype`属性值
  - 程序员能直接操作显示原型，但不能直接操作隐式原型（`ES6`之前）

## 原型链

### 原型链（图解）

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_原型链_内存图.png)

- 访问一个对象的属性时，
  - 先在自身属性中查找，找到返回
  - 如果自身属性中没有，再沿着`__proto__`这条链向上查找，找到返回
  - 如果最终没有找到，返回`undefined`
- 原型链的别名：隐式原型连
- 原型链的作用：查找对象的属性（方法）

### 构造函数/原型/实例对象的关系（图解）

`var o1 = new Object()`

`var o2 = {}`

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_构造函数原型实例对象关系.png)

### 构造函数/原型/实例对象的关系2（图解）

下面这个图需要仔细理解。

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_构造函数原型实例对象关系图2.png)

### 原型继承

- 构造函数的实例对象自动拥有构造函数原型对象的属性（方法）
- 利用的就是原型链

### 原型链——属性问题

- 读取对象的属性值时：会自动到原型链中查找
- 设置对象的属性值时：不会查找原型链，如果当前对象中没有此属性，直接添加此属性并设置其值
- 方法一般定义在原型中，属性一般通过构造函数定义在对象本身上。

## 探索 `instanceof`

### `instanceof`是如何判断的？

- 表达式：`A instanceof B`
- 如果`B`函数的显式原型对象在`A`对象的(隐式)原型链上，返回`true`，否则返回`false`

### `Function`是通过`new`自己产生的实例

### 案例1

```javascript
function Foo() {  }
var f1 = new Foo();
console.log(f1 instanceof Foo); // true
console.log(f1 instanceof Object); // true
```

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_Function原型图.png)

### 案例2(重要)

```javascript
console.log(Object instanceof Function); // true 
console.log(Object instanceof Object); // true
console.log(Function instanceof Function); // true
console.log(Function instanceof Object); // true

function Foo() {}
console.log(Object instanceof  Foo); // false
```

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_完整原型图.png)

注意：`Function`的显示原型和隐式原型是一样的。

## 面试题

### 测试题1

```javascript
var A = function() { }
A.prototype.n = 1
var b = new A()
A.prototype = {
  n: 2,
  m: 3
}
var c = new A()
console.log(b.n, b.m, c.n, c.m) // 1 undefined 2 3 
```

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_面试题内存图.png)

### 测试题2

```javascript
var F = function(){};
Object.prototype.a = function(){
  console.log('a()')
};
Function.prototype.b = function(){
  console.log('b()')
};
var f = new F();
f.a() // a()
f.b() // 报错  f.b is not a function
F.a() // a()
F.b() // b()
```

对照下图理解：

![](C:\Users\10992\Desktop\projectNotes\JavaScript高级深入总结\media\原型_完整原型图.png)

