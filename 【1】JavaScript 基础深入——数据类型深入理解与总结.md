### 【1】JavaScript基础深入——数据类型深入理解与总结

# 数据类型分类

## 基本（值）类型（5种）

- `String`：任意字符串
- `Number`：任意的数字
- `boolean`：`true`/`false`
- `null`：`null`
- `undefined`：`undefined`

## 对象（引用）类型（3种）

- `Object`：任意对象
- `Array`：一种特别的对象（数值下标，内部数据是有序的）
- `Function`：一种特别的对象（可以执行）

# 数据类型判断（3种方式）

## `typeof` ：返回数据类型的<u>字符串</u>表达

```javascript
var a
console.log(a) // undefined
console.log(typeof a) // "undefined"
console.log(a === undefined) // true
console.log(typeof a === undefined) // false
console.log(typeof a === "undefined") // true
console.log(undefined === "undefined") // false

a = 4
console.log(typeof a) // "number"
console.log(typeof a === Number) // false
console.log(typeof a === "number") // true

a = "hahha"
console.log(typeof a) // "string"

a = false
console.log(typeof a) // "boolean"

a = null
console.log(typeof a) // object
console.log(a === null) // true
```

注意：`typeof`返回的是数据类型的**字符串**表达形式。

```javascript
typeof true //"boolean"
typeof "hahha" //"string"
typeof 12 //"number"
typeof null //"object"
typeof ccc //"undefined"

typeof function(){} //"function"
typeof {} //"object"
```

## `instanceof`：类型的实例

> 首先要理解`instanceof`的含义：

- `instance` 是**例子**的意思，`A instanceof B `实际上是判断`A`是否是`B`的一个***实例***。理解了这一点，就不难判断类型了。

```javascript
var b1 = {
    b2: [1, "hehe", console.log],
    b3: function () {
        console.log("b3")
        return function  () {
            return "Mandy"
        }
    }
}

console.log(b1 instanceof Object) // true
console.log(b1.b2 instanceof Array, b1.b2 instanceof Object) // true true
console.log(b1.b3 instanceof Function, b1.b3 instanceof Object) //true true

console.log(typeof b1.b2) // "object"
console.log(typeof b1.b3) // "function"
console.log(typeof b1.b2[1]) // "string"
console.log(typeof b1.b2[2]) // "function"
b1.b2[2](555) // 555
console.log(b1.b3()()) // "b3" "Mandy"
```

注意：

- 函数既是 `Function `类型，也是 `Object` 类型
- 数组既是 `Array `类型，也是 `Object `类型

## `===`

- 可以判断`undefined `和 `null`

```javascript
ccc === "undefined" // true
null === null // true
```

## 总结

- `typeof `:

  - 可以判断 `undefined` / 数值 / 字符串 / 布尔值 / `function`

  - 不能判断 `null` 与 `object`,   `array` 与 `object`

    ```javascript
    typeof null // "object"
    
    typeof [] // "object"
    ```

- `instanceof`:
  - 判断对象的具体类型
  - `A instanceof B`

- `===`:

  - 可以判断 `undefined`  , `null`

# `undefined` 与 `null` 的区别？

- `undefined` 代表定义了，未赋值

- `null` 代表定义了，并且赋值了，只是赋的值为` null`

```javascript
// undefined与null的区别?
var a
console.log(a)  // undefined
a = null
console.log(a) // null
```

# 什么时候给变量赋值为`null`?

- 初始赋值，表明将要赋值为对象。因为 `typeof null === "Object"  `
- 结束前，让对象成为垃圾对象（被垃圾回收器回收）

```javascript
//起始
var b = null  // 初始赋值为null, 表明将要赋值为对象
//确定对象就赋值
b = ['atguigu', 12]
//最后
b = null // 让b指向的对象成为垃圾对象(被垃圾回收器回收)
```

# 严格区别*变量类型* 与 *数据类型*？

- 数据的类型：
  - 基本类型
  - 对象类型
- 变量的类型（变量内存值的类型）
  - 基本（值）类型：保存的就是基本类型的数据
  - 引用类型：保存的是地址值

# 理解<u>实例</u>与<u>类型</u>

```javascript
// 实例: 实例对象
// 类型: 类型对象
function Person (name, age) {// 构造函数  类型
  this.name = name
  this.age = age
}
var p = new Person('tom', 12) // 根据类型创建的实例对象
```

