# 《JavaScript 高级程序设计》第六章 : 深入 Object 类型

## 简介

在第五章我们已经学习了 `Object` 的定义，成员的组成、访问、引用类型与对象、类与构造函数等基础概念，同时也单独对内置的引用类型进行了详细的说明，而这一章我们将单独的对 `Object` 进行更深入的学习，使我们对 Object 类型有一个整体且全面的了解。

本章主要内容有：

- 对象更高级的创建方式
- 对象继承的原理与常用的继承种类
- 对象属性的分类以及相关的属性运算符
- 掌握与了解实例对象所继承的方法或属性
- 掌握与了解 `Object` 构造函数的静态属性与静态方法。

## 创建对象

[略](../)

## 对象继承

[略](../)

## 对象属性

[略](../)

## 实例对象的继承方法

实例对象的继承方法指的是从其构造函数（这里主要是 Object ）原型对象属性 `prototype` 上继承来的方法。

```javascript
var obj = new Object();
obj.a = 1;
```

> obj 为 “该对象”

### constructor

```javascript
obj.constructor() === obj; //false
```

返回创建该实例对象的构造函数。

### hasOwnProperty

```javascript
obj.hasOwnProperty("a"); //true
obj.hasOwnProperty("b"); //false
```

判断指定属性是否为该对象的实例属性，返回值为布尔值。

需要注意的是该继承方法无法判断不存在的属性。

### isPrototypeOf

```javascript
obj.isPrototypeOf({}); //false
obj.constructor.prototype.isPrototypeOf(obj); //true
```

判断该对象是否在指定对象的原型链中，或者说指定对象是否继承于该对象 `obj`。

### propertyIsEnumerable

```javascript
obj.propertyIsEnumerable("a"); //true
```

判断该对象上的指定属性是否为可枚举，返回值为布尔值。

`obj.propertyIsEnumerable` 方法返回的值会影响对象属性是否能够被 `Object.keys()` 与 `for..in` 遍历。

需要注意的是 `propertyIsEnumable` 方法无法判断对象的继承属性。

```javascript
Object.prototype.label = 2;
var obj = new Object();

obj.propertyIsEnumerable("label"); //false
```

> 个人认为主要的原因在于对象的 `__proto__` 属性是一个内置的隐藏属性，而且还是不可枚举。

我们可以通过 `Object.defineProperty()` 或者 `Object.defineProperties()` 等方法设置 `enumerable` 属性描述符来影响 `Object.propertyIsEnumerable()` 的返回值。

### toString

由于对象实例会继承构造函数 `Object` 的 `prototype`对象，所以关于对象实例的 `toString` 方法说明，一律使用 `Object.prototype.toString` 来代替。

```javascript
obj.toString === Object.prototype.toString; //true
```

`Object.prototype.toString`  会采用固定的转换格式，并拼接上对象内部的 `Class`  属性的值，从而返回一个标识对象分类（类型）的字符串。 其转换结果的格式为 `[object [[Class]] ]`  。其中[[Class]] 属性是对象的内部属性，表明了该对象的类型。除了使用 `Object.prototype.toString`  方法可以获取 [Class]] 属性的值，没有其它的访问方式。

ECMAScript 5 中关于 `Object.prototype.toString` 方法规范如下：

1. 如果 this 的值是 undefined, 返回 "[object Undefined]".
2. 如果 this 的值是 null, 返回 "[object Null]".
3. 令 O 为以 this 作为参数调用 ToObject(this) 的结果 .
4. 令 class 为 O 的 [[Class]] 内部属性的值 .
5. 返回三个字符串 "[object ", class "]" 连起来的字符串 .

> `O`  是调用 Object.prototype.toString(O) 传入的作为 this 的参数，`toObject` 是 JS 引擎内的对象转换方法，可以将参数转换对应的对象类型。一般当我们将 `Object` 作为转换方法时调用，这也是将 `Object` 归类为工厂方法模式的根本原因（即根据参数返回不同类型的对象）。

```javascript
Object.prototype.toString.call({}); //"[object Object]"
Object.prototype.toString.call([]); //"[object Array]"
Object.prototype.toString.call(""); //"[object String]"
Object.prototype.toString.call(1); //"[object Number]"
Object.prototype.toString.call(new Number()); //"[object Number]"
Object.prototype.toString.call(true); //"[object Boolean]"
Object.prototype.toString.call(null); //"[object Null]"
Object.prototype.toString.call(undefined); //"[object Object]"
```

具体封装如下：

```javascript
function isType(target, type) {
  return Object.prototype.toString.call(target) === "[object " + type + "]";
}

isType([], "Array");
```

由于内置属性 `[[Class]]` 只存在在内置对象(ECMAScript 原生对象和宿主对象)中，所以此种方法无法检测自定义构造函数返回的自定义对象。对于自定义引用类型我们可以使用 `instanceof`  操作符或者是 `isPrototypeOf()`  方法。

```javascript
Object.prototype.toString.call(obj); //"[object Object]"
Object.prototype.toString.call(obj1); //"[object Object]"
```

现在我们可以总结下关于 `Object.prototype.toString` 的使用情况：

- 只有内置对象才具有 `[[Class]]` 属性，所以只适用于内置对象的类型判断。
- 利用了 `Object.prototype.toString` 返回结果的固定格式，无需做额外的判断。
- 利用了 `[[Class]]` 返回一个表明对象类型的字符串值。

> 这种使用方式我称之为“跨类型的方法借用”，即任意一种类型的值都可以将自己作为 `this`  来调用其它类型的方法，另外一个典型的例子就是 `Array.prototype.slice.call(arguments)` .

### valueOf

返回该实例对象自身的引用。

```javascript
obj.valueOf() === obj; //true
```

## 构造函数静态方法

下面都是 `Object` 构造函数的静态方法，可以通过构造函数直接调用。

### create

`create: ƒ create(o, properties)` 可根据指定的对象为原型（模版）创建新的对象，其中 `o` 为作为模版的原型对象，而 `properties` 则是为对象新增加的具有特征描述的属性，其格式与 `Object.defineProperties(o, properties)` 方法的第二个参数完全相同。

`Object.create()` 方法也可以看作是对“原型式继承”的官方规范化实现。

### keys

`keys: ƒ keys(o)` 将指定对象的所有可枚举属性的 key 保存在一个数组中。

### defineProperties

`defineProperties: ƒ defineProperties(o, properties)` 方法可以为指定对象批量设置对象属性描述符，也可以批量添加具有指定属性描述符的新属性。

### defineProperty

`defineProperty : ƒ defineProperty(o, propertyKey, attributes)` 其功能与 `Object.defineProperties` 完全相同，只是该方法无法批量修改对象的属性描述符，也无法批量新增具有属性描述符的新属性，其中 `o` 为要操作的对象， `propertyKey` 为要设置的属性名称，而 `attributes` 则是要设置属性的特征描述符。

### getOwnPropertyDescriptor

`getOwnPropertyDescriptor: ƒ getOwnPropertyDescriptor(o, propertyKey)` 可用来获取指定对象的指定属性的特征描述符，其中 `o` 是特定的对象，而 `propertyKey` 则是对象上指定的属性。

### getOwnPropertyDescriptors

`getOwnPropertyDescriptors: ƒ getOwnPropertyDescriptors(o)` 可一次性获取该对象上所有可枚举属性的特征描述符。

```javascript
Object.getOwnPropertyDescriptors(Math);
```

### getOwnPropertyNames

`getOwnPropertyNames: ƒ getOwnPropertyNames(o)` 该方法可以获取指定对象上的所有可枚举与不可枚举的实例属性（但不包括隐藏属性，例如 `__proto__` 以及 `setter` `getter` 等）。

### getPrototypeOf

`getPrototypeOf: ƒ getPrototypeOf(o)` 可以获取指定参数对象的原型对象，也即该对象 `__proto__` 属性所指向的关联（继承）对象。

### preventExtensions

`preventExtensions: ƒ preventExtensions(o)` 该方法会让一个对象变为“不可扩展”的对象，也就是永远不能为该对象添加新的属性成员。

对不可扩展对象添加新的对象属性，在非严格模式下会静默失败的执行，而在严格模式下则会抛出 `Type Error` 错误。

```
Uncaught TypeError: Cannot add property a, object is not extensible
```

由于 `Object.preventExtensions()` 方法返回的值依然是对象本身，所以通常我们并不需要专门去保存其返回值。

```javascript
var obj = {};
Object.preventExtensions(obj) === obj; //true
```

总的来说 `preventExtensions()` 方法主要影响对象的：

- 增 : 无法新增属性

> `preventExtensions()` 方法不会影响含有引用类型值的对象属性。

### seal

`seal: ƒ seal()` 该方法可以“密封”一个对象，即该对象无法新增属性，对象属性的描述符也会被设置为不可配置，即对象属性无法删除，更无法通过 `defineProperty` 等来修改属性的特征描述，直接产生的影响就是无法将“数据属性”转换为 “访问器属性” 或者相反的转换。

该方法返回值是一个被密封的对象，返回对象也是对原对象的引用，所以通常我们无需专门保存。

在非严格模式下对密封对象进行限制项相关的操作（新增属性、修改属性描述符、删除属性）会静默失败执行，而在严格模式则会产生 `Type Error` 错误。

总的来说 `Object.seal(o)` 方法主要影响：

- 增 : 无法新增
- 删 : 无法删除
- 属性描述符 : 不可配置 `configurable:false`

```javascript
var obj = { a: 1 };
Object.defineProperty(obj, "a", {
  configurable: true
});
Object.seal(obj);
delete obj.a; //false
```

因此我们看出 `seal` 是在 `preventExtensions` 不可扩展的基础上还增加了不可删除、不可配置的限制。

> `Object.seal()` 方法不会影响值为引用类型的对象属性，但密封对象的 `__proto__` 原型链属性将无法修改。

### freeze

`freeze: ƒ freeze(o)` 该方法会”冻结“一个对象，即该对象无法新增属性、无法删除属性、也无法修改属性的值，所有特征描述符都不可修改（可配置性、可枚举性、可写性、默认值）等。

被冻结的对象，其原型也无法修改，由于该方法返回的冻结对象依然是对原对象的引用，所以通常无需保存。

在非严格模式，如果对冻结对象进行限制项相关的操作，则会静默失败执行，而在严格模式下则会产生 `Type Error` 错误。

总的来说 `Object.freeze()` 方法主要影响：

- 增 ：无法新增属性
- 删 ：无法删除属性
- 改 ：无法删除
- 属性描述符：不可配置、不可写、不可枚举
- 转：数据属性无法转换为访问器属性。

```javascript
function a() {}
Object.freeze(a);

a.sayName = function() {
  return this.name;
};
console.log(a.sayName); //undefined

var b = [3, 2, 1];
Object.freeze(b);

b.sort();
//Uncaught TypeError: Cannot assign to read only property '0' of object '[object Array]'
console.log(b);
```

因此 `freeze` 是在 `seal` 与 `preventExtensions` 等基础上再限制了可枚举性、可写性。

> `Object.freeze()` 方法不会影响含有引用值的对象属性。

### isExtensible

`isExtensible: ƒ isExtensible(o)` 判断指定的对象是否为“不可扩展对象”，返回值为布尔值。

### isSealed

`isSealed: ƒ isSealed(o)` 判断指定的对象是否为“密封对象”，返回值为布尔值。

### isFrozen

`isFrozen : ƒ isFrozen(o)` 判断指定的对象是否为“冻结对象”，返回值为布尔值。

## 构造函数静态属性

下面都是 `Object` 构造函数的静态属性。

### name

构造函数的名称，其值就是函数名 —— `Object`。

### prototype

`Object` 构造函数的原型对象，可以被其实例对象所继承。

```javascript
{
    constructor: ƒ Object()
    hasOwnProperty: ƒ hasOwnProperty()
    isPrototypeOf: ƒ isPrototypeOf()
    propertyIsEnumerable: ƒ propertyIsEnumerable()
    toLocaleString: ƒ toLocaleString()
    toString: ƒ toString()
    valueOf: ƒ valueOf()
    __defineGetter__: ƒ __defineGetter__()
    __defineSetter__: ƒ __defineSetter__()
    __lookupGetter__: ƒ __lookupGetter__()
    __lookupSetter__: ƒ __lookupSetter__()
    get __proto__: ƒ __proto__()
    set __proto__: ƒ __proto__()
}
```

### **proto**

我们前面说过，函数也是对象，也需要一个构造函数来创建，所以 `Object` 构造函数对象的原型链属性关联的就是 `Function.prototype` 对象。

```javascript
Object.__proto__ === Function.prototype; //true
```

下面我们打印 `Function.prototype` 的内容，看看它到底有哪些成员属性。

```javascript
console.dir(Function.prototype);

ƒ anonymous()
    apply: ƒ apply()
    arguments: (...)
    bind: ƒ bind()
    call: ƒ call()
    caller: (...)
    constructor: ƒ Function()
    length: 0
    name: ""
    toString: ƒ toString()
    get arguments: ƒ ()
    set arguments: ƒ ()
    get caller: ƒ ()
    set caller: ƒ ()
    __proto__: Object
```

首先我们可以发现构造函数的原型对象是一个匿名的函数对象，并且这个匿名的函数上具有我们常用的一些 `call`、`bind`、`apply`、`toString` 等方法，因此我们可以得知不论构造函数还是普通的函数，其实都继承至这个匿名的函数对象，从而继承了 `call`、`bind`、`apply`、`toString` 等方法。

```javascript
function test() {}

console.dir(test);

ƒ test()
    arguments: null
    caller: null
    length: 0
    name: "test"
    prototype: {constructor: ƒ}
    __proto__: ƒ ()
        apply: ƒ apply()
        arguments: (...)
        bind: ƒ bind()
        call: ƒ call()
        caller: (...)
        constructor: ƒ Function()
        length: 0
        name: ""
        get arguments: ƒ ()
        set arguments: ƒ ()
        get caller: ƒ ()
        set caller: ƒ ()
        __proto__: Object
```

也因此让我明白了为什么 `Object.toString()` 与 `new Object().toString()` 的返回结果不一致的问题，原因很简单，这两个 `toString()` 方法根本就不是一个方法，前者是 `Object` 构造函数继承而来的，而后者则是其原型对象上所定义的然后被其实例对象所继承。

其实还有一个蛮有意思的点，那就是匿名的函数对象本身又继承自 `Object` 构造函数的原型对象。

```javascript
Object.__proto__.__proto__ === Object.prototype; //true
```
