# `JavaScript `面向对象高级——继承模式

## 一、原型链继承

方式1: 原型链继承

（1）流程：

​		1、定义父类型构造函数。

​		2、给父类型的原型添加方法。

​		3、定义子类型的构造函数。

​		4、创建父类型的对象赋值给子类型的原型。

​		5、将子类型原型的构造属性设置为子类型。

​		6、给子类型原型添加方法。

​		7、创建子类型的对象: 可以调用父类型的方法。

  （2）关键：

- 子类型的原型为父类型的一个实例对象

```javascript
// 1.定义父类型构造函数
function Supper() {
  this.supProp = 'Supper property'
}
// 2.给父类型的原型添加方法
Supper.prototype.showSupperProp = function () {
  console.log(this.supProp)
}
// 3.定义子类型的构造函数
function Sub() {
  this.subProp = 'Sub property'
}
// 4.子类型的原型为父类型的一个实例对象
Sub.prototype = new Supper()
// 5.将子类型原型的构造属性constructor指向子类型
Sub.prototype.constructor = Sub
// 6.给子类型原型添加方法
Sub.prototype.showSubProp = function () {
  console.log(this.subProp)
}
// 7.创建子类型的对象，可以调用父类型的方法
var sub = new Sub()
sub.showSupperProp()  // Supper property
sub.showSubProp()  // Sub property
console.log(sub)  // Sub
```

![](.\media\原型链继承.png)

## 二、借用构造函数继承

方式2: 借用构造函数继承。

（1）流程：

​		1、定义父类型构造函数。

​		2、定义子类型构造函数。

​		3、在子类型构造函数中调用父类型构造。

```javascript
// 1.定义父类型构造函数
function Person(name, age) {
  this.name = name
  this.age = age
}
// 2.定义子类型构造函数
function Student(name, age, price) {
  // 3.在子类型构造函数中调用父类型构造
  Person.call(this, name, age)  // 相当于: this.Person(name, age)
  /*this.name = name
  this.age = age*/
  this.price = price
}

var s = new Student('Tom', 20, 14000)
console.log(s.name, s.age, s.price) // Tom 20 14000
```

（2）关键:

- 在子类型构造函数中通过`call()`调用父类型构造函数。

## 三、组合继承

方式3: 原型链+借用构造函数的组合继承。

1、利用原型链实现对父类型对象的方法继承。

2、利用`call()`借用父类型构造函数初始化相同属性。

```javascript
function Person(name, age) {
  this.name = name
  this.age = age
}

Person.prototype.setName = function (name) {
  this.name = name
}

function Student(name, age, price) {
  Person.call(this, name, age)  // 为了得到属性
  this.price = price
}

Student.prototype = new Person() // 为了能看到父类型的方法
Student.prototype.constructor = Student //修正constructor属性
Student.prototype.setPrice = function (price) {
  this.price = price
}

var s = new Student('Tom', 24, 15000)
s.setName('Bob')
s.setPrice(16000)
console.log(s.name, s.age, s.price)
```

