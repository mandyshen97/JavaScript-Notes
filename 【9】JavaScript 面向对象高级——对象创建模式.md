# `JavaScript`面向对象高级——对象创建模式

## 一、工厂模式

- 流程: 定义一个函数，函数返回对象。
- 适用场景: 需要创建多个对象，都是`Object`类型。
- 优点：完成了返回一个对象的要求。
-  缺点: 对象没有一个具体的类型，无法通过`constructor`识别对象, 都是`Object`类型。多个实例的`sayName`方法都是实现一样的效果，但是却存储了很多次。

```javascript
// 1.工厂模式
function createPerson (name, age, job) { // 返回一个对象的函数==》工厂函数
  // 显式的创建对象
  var o = new Object()
  o.name = name
  o.age = age
  o.job = job
  o.sayName = function () {
    alert(this.name)
  }
  return o // 返回对象
}
var p1 = createPerson('Tom', 12, 'student')
var p2 = createPerson('Mandy', 22, 'teacher')

// 或者：
function createPerson (name, age, job) { // 返回一个对象的函数==》工厂函数
  // 显式的创建对象      
  var o = {
    name:name,
    age:age,
    job:job,
    sayName: function () {
      alert(this.name)
    }
  }
  return o // 返回对象
}
var p1 = createPerson('Tom', 12, 'student')
var p2 = createPerson('Mandy', 22, 'teacher'
```

## 二、.构造函数模式

- 流程：创建一个构造函数，没有显式地创建对象，没有`return`语句，通过`new`操作符创建对象。
- 使用场景：需要创建多个**类型确定**的对象。
- 优点：可以通过`constructor`或者`instanceof`来识别对象实例的类型。
- 缺点：多个实例的`sayName`方法都是实现一样的效果，但是却存储了很多次（两个对象实例的`sayName`方法是不同的，因为存放的地址不同）。

```javascript
function Person (name, age) {
  this.name = name
  this.age = age
  this.sayName = function () {
    alert(this.name)
  }
}
var p1 = new Person('Tony', 14)
var p2 = new Person('Bob', 15
```

## 三、原型模式

- 流程：创建一个构造函数，给这个函数的`prototype`添加属性和方法。通过new操作符创建对象。
- 使用场景：起始时对象内部数据是确定的。
- 优点：（1）`sayName`方法是共享的，所有实例的`sayName`方法都指向同一个。（2）可以动态的添加原型对象的方法和属性，并直接反映在对象实例上。
- 缺点：（1）由于`p1`和`p2`的`name`属性指向同一块内存区域，因此改变`p1.name`会导致`p2.name`改变。（2）所有的方法都是共享的，没有办法创建自己的属性和方法，也没有办法像构造函数哪像传递参数。

```javascript
function Person () {}
Person.prototype.name = 'Mandy'
Person.prototype.age = 16
Person.prototype.sayName = function () {
  alert(this.name)
}
var p1 = new Person()
var p2 = new Person()
p1.sayName() // "Mandy"
```

## 四、构造函数+原型组合模式

- 流程：自定义构造函数，属性在函数中初始化，方法添加到原型上。
- 适用场景：需要创建多个类型确定的对象。
- 优点：（1）解决了原型模式对于对象引用的问题。（2）解决了原型模式没有办法传递参数的问题。（3）解决了构造函数模式不能共享方法的问题。

```javascript
function Person (name) {
  this.name = name
}
Person.prototype.sayName = function () {
  console.log(this.name)
}
var p1 = new Person('Mandy')
p1.sayName() // Mandy
```

## 五、动态原型模式

- 流程：创建构造函数，在构造函数内部初始化属性，**在构造函数内部在原型上添加方法**。通过`new`操作符创建对象。
- 需要创建多个类型确定的对象。
- 优点：（1）可以在初次调用构造函数的时候就完成原型对象的修改。（2）对原型对象的修改能在所有的实例中反映。
- 缺点：红宝书上说了这个方案非常完美。

```javascript
function Person(name, age) {
  // 属性
  this.name = name
  this.age = age
  // 方法
  // 判断sayName方法不存在的情况下，在原型上添加sayName方法。
  if(typeof this.sayName != 'function') {
    Person.prototype.sayName = function () {
      console.log(this.name)
    }
  }
}
var p1 = new Person('Tom')
p1.sayName() // "Tom
```

## 六、寄生构造模式

- 流程：创建一个构造函数，在这个函数内部创建一个对象，用`return`返回对象。通过`new`操作符创建。
- 除了使用`new`操作符并把使用的包装函数叫做构造函数以外，这个模式与工程模式其实是一模一样的。

```javascript
function Person(name, age) { // 将创建对象的代码封装在函数中。
  // 显式创建要返回的对象
  var o = new Object()
  o.name = name
  o.age = age
  o.sayName = function () {
    console.log(this.name)
  }
  return o   // 返回对象
}
var p1 = new Person('Mandy',18)
```

## 七、稳妥构造模式

- 优点：安全。除了调用`sayName`方法外，没有其他办法可以访问`name`的值。

```javascript
function Person (name, age) {
  // 创建要返回的对象
  var o = new Object()
  // 添加方法
  o.sayName = function () {
    console.log(this.name)
  }
  // 返回对象
  return o
}
var p1 = new Person('Mandy',20)
p1.name  // undefined
// 除了调用sayName方法外，没有其他办法可以访问name的值。
p1.sayName() // "Mandy"
```

