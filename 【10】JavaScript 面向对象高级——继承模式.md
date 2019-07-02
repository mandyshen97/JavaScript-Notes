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

（3）缺点：

1、包含引用类型值的原型，由于实例共享属性，一个实例对原型属性的修改会在另一个实例中反映出来。

2、不能向超类型的构造函数中传递参数。

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

（3）缺点：

1、无法避免构造函数模式存在的问题——方法都在构造函数中定义，无法函数复用。

2、在父类型原型中定义的方法在子类型中是不可见的，只能继承父类型构造函数中定义的属性和方法。

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

## 四、原型式继承

```javascript
function createObj(o) {
    function F(){}
    F.prototype = o;
    return new F();
}
```

就是 `ES5 Object.create` 的模拟实现，将传入的对象作为创建的对象的原型。

缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。

```javascript
var person = {
    name: 'kevin',
    friends: ['daisy', 'kelly']
}

var person1 = createObj(person);
var person2 = createObj(person);

person1.name = 'person1';
console.log(person2.name); // kevin
person1.firends.push('taylor');
console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

注意：修改`person1.name`的值，`person2.name`的值并未发生改变，并不是因为`person1`和`person2`有独立的 `name` 值，而是因为`person1.name = 'person1'`，给`person1`添加了 `name `值，并非修改了原型上的 `name `值。

## 五、寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

```javascript
function createObj (o) {
    var clone = Object.create(o);
    clone.sayName = function () {
        console.log('hi');
    }
    return clone;
}
```

缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

## 六、寄生组合式继承

在这里重复一下组合继承的代码：

```javascript
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

Child.prototype = new Parent();

var child1 = new Child('kevin', '18');

console.log(child1)
```

组合继承最大的缺点是会调用两次父构造函数。

一次是设置子类型实例的原型的时候：

```
Child.prototype = new Parent();
```

一次在创建子类型实例的时候：

```
var child1 = new Child('kevin', '18');
```

回想下 new 的模拟实现，其实在这句中，我们会执行：

```
Parent.call(this, name);
```

在这里，我们又会调用了一次 `Parent` 构造函数。

所以，在这个例子中，如果我们打印 `child1` 对象，我们会发现 `Child.prototype` 和 `child1 `都有一个属性为`colors`，属性值为`['red', 'blue', 'green']`。

那么我们该如何精益求精，避免这一次重复调用呢？

如果我们不使用 `Child.prototype = new Parent() `，而是间接的让` Child.prototype `访问到 `Parent.prototype` 呢？

看看如何实现：

```javascript
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

// 关键的三步
var F = function () {};

F.prototype = Parent.prototype;

Child.prototype = new F();


var child1 = new Child('kevin', '18');

console.log(child1);
```

最后我们封装一下这个继承方法：

```javascript
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function prototype(child, parent) {
    var prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}

// 当我们使用的时候：
prototype(Child, Parent);
```

引用《JavaScript高级程序设计》中对寄生组合式继承的夸赞就是：

这种方式的高效率体现它只调用了一次 `Parent `构造函数，并且因此避免了在` Parent.prototype` 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用` instanceof` 和 `isPrototypeOf`。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。