# `JavaScript`面向对象高级——对象创建模式

## 一、`Object`构造函数模式

方式1: `Object`构造函数模式

- 流程: 先创建空`Object`对象, 再动态添加属性/方法。

- 适用场景: 起始时不确定对象内部数据。

- 问题: 语句太多。

```javascript
/*
一个人: name:"Tom", age: 12
 */
// 先创建空Object对象
var p = new Object()
p = {} //此时内部数据是不确定的
// 再动态添加属性/方法
p.name = 'Tom'
p.age = 12
p.setName = function (name) {
  this.name = name
}

//测试
console.log(p.name, p.age) // Tom 12
p.setName('Bob')
console.log(p.name, p.age) // Bob 12
```

## 二、对象字面量模式

方式2: 对象字面量模式

- 流程: 使用`{}`创建对象, 同时指定属性/方法。

- 适用场景: 起始时对象内部数据是确定的。

- 问题: 如果创建多个对象, 有重复代码。

```javascript
var p = {
  name: 'Tom',
  age: 12,
  setName: function (name) {
    this.name = name
  }
}

//测试
console.log(p.name, p.age)  // Tom 12
p.setName('JACK')
console.log(p.name, p.age)  // JACK 12

var p2 = {  //如果创建多个对象代码很重复
  name: 'Bob',
  age: 13,
  setName: function (name) {
    this.name = name
  }
}
```

## 三、工厂模式

方式3: 工厂模式

- 流程: 通过工厂函数动态创建对象并返回。

- 适用场景: 需要创建多个对象。

- 问题: 对象没有一个具体的类型, 都是`Object`类型。

```javascript
function createPerson(name, age) { //返回一个对象的函数===>工厂函数
  var obj = {
    name: name,
    age: age,
    setName: function (name) {
      this.name = name
    }
  }
  return obj
}

// 创建2个人
var p1 = createPerson('Tom', 12)
var p2 = createPerson('Bob', 13)
// p1/p2是Object类型

function createStudent(name, price) {
  var obj = {
    name: name,
    price: price
  }
  return obj
}

var s = createStudent('张三', 12000)
// s也是Object类型
```

## 四、自定义构造函数模式

方式4: 自定义构造函数模式

- 流程: 自定义构造函数, 通过`new`创建对象。

- 适用场景: 需要创建多个类型确定的对象。

- 问题: 每个对象都有相同的数据, 浪费内存。

```javascript
//定义类型
function Person(name, age) {
  this.name = name
  this.age = age
  this.setName = function (name) {
    this.name = name
  }
}

var p1 = new Person('Tom', 12)
p1.setName('Jack')
console.log(p1.name, p1.age)  // Jack 12
console.log(p1 instanceof Person)  // true

// 定义类型
function Student (name, price) {
  this.name = name
  this.price = price
}

var s = new Student('Bob', 13000)
console.log(s instanceof Student)  // true

var p2 = new Person('JACK', 23)
console.log(p1, p2) // Person{age:12, name:"Jack",...}  Person{age:23, name:"Jack",...}
```

## 五、组合模式（构造函数+原型）

方式5: 构造函数+原型的组合模式

- 流程: 自定义构造函数, 属性在函数中初始化, 方法添加到原型上。

- 适用场景: 需要创建多个类型确定的对象。

```javascript
function Person(name, age) { //在构造函数中只初始化一般函数
  this.name = name
  this.age = age
}

Person.prototype.setName = function (name) {
  this.name = name
}

var p1 = new Person('Tom', 23)
var p2 = new Person('Jack', 24)
console.log(p1, p2) 
```

