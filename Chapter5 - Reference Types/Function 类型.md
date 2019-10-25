## 《JavaScript 高级程序设计》第五章：Function 类型

函数也是对象，每个函数都是 `Function` 类型的实例，只是函数的书写格式与普通对象的格式存在着很大的区别。

函数是对象，所以函数也具有属性和方法，例如：`length`、`prototype`、`bind`、`call`、`apply`等，更可以自定义属性和方法。

函数的类型有两种：“声明式函数”与“表达式函数”。函数名只是一个变量“标识符”而已，其保存的是对函数引用的指针，并非函数本身。

```javascript
var a = function() {};
var b = a;

a = null;

typeof a; //null;
typeof b; //function
```

证明一个函数就是对象的最简单的办法就是打印一个函数的详细内容：

```javascript
function define() {}
console.dir(define);

/*
    arguments: null
    caller: null
    length: 0
    name: "define"
    prototype: {constructor: ƒ}
    __proto__: ƒ ()
*/
```

从实例代码中，我们可以发现创建的 `define` 函数也具有 `__proto__` 原型链属性和 `prototype` 原型属性，想必这说明了为什么“函数”是 JavaScript  的一等公民的原因。

其次也可以通过 `instanceof`  运算符进行判断。

```javascript
define instanceof Object(); //true
```

## 作为值的函数

函数名也是“标识符”，其性质类似于变量，存储的是对函数引用的指针。

当函数名没有结合一对圆括号`()` 进行调用使用时，函数“指针”就可以作为值的形式通过函数名来进行赋值与传递。

示例中 `HOF` 类似于一个高阶函数，接收一个函数作为参数，并返回一个新的函数。

```javascript
function HOF(fun) {
  var params = {
    key: "HOF"
  };
  return fun.bind(params);
}

var say = function() {
  alert("my is " + this.key);
};

var result = HOF(say);

result(); //my is HOF
```

“作为值的函数” 另一种广泛使用的场景便是回调函数了，例如定义一个比较函数作为值来传入到 `sort` 方法中。

```javascript
var data = [
  { name: "Zachary", age: 28 },
  { name: "Nicholas", age: 22 },
  { name: "Kims", age: 21 },
  { name: "Time", age: 20 }
];

function createCompareFunction(fieldName) {
  return function(first, second) {
    if (first[fieldName] < second[fieldName]) {
      return -1;
    } else if (first[fieldName] > second[fieldName]) {
      return 1;
    } else {
      return 0;
    }
  };
}

data.sort(createCompareFunction("name")); //按照名称升序。
data.sort(createCompareFunction("age")); //按照年龄升序。
```

## 函数的声明提升

函数与变量一样存在着声明提升。

只有“声明式函数”存在着函数声明提升（function declaration hoisting）的特性。“函数表达式”则不存在这种情况。究其原因还是因为“函数表达式”符合变量声明与变量赋值的范畴。

```javascript
sum(1, 2); //3
reduce(2, 1); //Uncaught TypeError: reduce is not a function

function sum(a, b) {
  return a + b;
}
var reduce = function(a, b) {
  return a - b;
};
```

当解析器向执行环境加载代码数据时，JavaScript 引擎会将需要声明的代码放在其它源代码树的顶部，在执行代码之前进行声明操作。也就是说，函数声明要早于普通代码的执行，上面的示例代码，实际情况如下所示：

```javascript
//setp1:声明函数
function sum(a, b) {
    return a + b;
}

//step2:声明变量
var reduce = ;

//step3 执行函数
sum(1, 2) //3
reduce(2, 1) //Uncaught TypeError: reduce is not a function

//step4 变量赋值

reduce = function(a, b) { return a - b; }
```

## 函数内部的对象

函数的内部有两个非常重要的对象：`this` 与 `arguments`。

`this` 指向的是函数或方法的调用对象，当函数在全局环境中被执行时 `this` 一般指向的是 `window` 对象，而当函数作为某个对象的方法被调用时，则 `this` 指向的就是这个对象本身。

```javascript
var a = function(){this//window}
var b = {
    a:function(){ this //<Object,b>}
}
```

> 总结下：函数中的 `this` 通常指向的是 `window`，而作为方法中的 `this` 指向的则是这个方法所依附的对象本身。

在严格模式 `use strict` 下，函数内部的 `this` 将不会指向 `window`，而是 `null`。

```javascript
"use strict";
function a() {
  this; //undefined
}
```

- 全局作用域中的 this 在严格模式下指向的是 null，在非严格模式下指向的是 window
- 当函数在全局环境中被执行时 `this` 一般指向的是 `window` 对象，在严格模式下同样是 null
- 方法中的 this 指向的是调用这个方法的对象本身
- [ES6]箭头函数中的 this 指向的是上个作用域中的 this。

关于 `arguments` 在第三章已经有过较为详细的说明，它是一个 `Like Array` 形式的对象。保存的是当前函数或方法的参数，其本身有一个 `length` 属性可以获取当前函数或方法的形参数量，除此之外，还有一个 `callee` 属性，返回对拥有这个 `arguments` 对象的函数自身的引用，通过 `callee` 属性我们可以运用“递归”方式，来实现一个阶乘的示例：

```javascript
function factory(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1);
  }
}
```

在某些需要引用自身的使用场景中 `callee` 属性可以非常好的减少代码的耦合度，另外 `callee` 属性自身还拥有一个`caller` 属性。

当拥有 `arguments` 对象的函数存在着被嵌套调用的情况。`caller` 属性的返回值就是这个函数的父函数的引用。

```javascript
function b() {
  arguments.callee; //ƒ b(){ arguments.callee; arguments.callee.caller;}
  arguments.callee.caller; //ƒ a(){ b();}
}

function a() {
  b();
}

a();
```

既然函数内部的 `arguments.callee` 返回的是函数自身，那么实际上函数本身也可以调用 `caller` 属性，即：`b.calleer`。

为了安全起见，ECMAScript 规定 `caller` 只有在被嵌套的函数上才会生效，普通的调用只会返回　`null`。

这样的设定也是为了防止同一环境中第三方代码对我们源码的窥探。

```javascript
function a() {}
a.caller; // null
```

> ES5 严格模式下 `arguments.callee` 属性会被拒绝访问，并且抛出 `TypeError` 的类型错误。

当在一个方法内部调用另一个方法，我们还可以将 `arguments`  对象整体的作为一个参数，传递内部被调用的方法。

```javascript
function b(b1, b2) {
  console.log(b1, b2);
}
function a(a1, a2) {
  b.apply(null, arguments);
}
a(1, 2);
//1,2
```

在这个示例中 `apply`  方法会像分解数组一样会去分解 `arguments`  对象。如果我们想将一个 `arguments`  对象转换为数组，则可以借用数组的 `slice`  方法。

```javascript
Array.prototype.slice.call(arguments, 0);
```

## 函数的属性与方法

**length**
length 返回函数当前的形参个数，其功能与 `arguments.length` 相同。

```javascript
function a(){}
function b(a){}
function c(a,b){}

a.length //0
b.length //1
c.length 2
```

**prototype**

`prototype` 对象上定义的属性与方法都是用与被其实例对象(new 运算符)所继承，因此普通函数也可以充当构造器函数使用。

```javascript
function a() {}
a.prototype.sayHello = function() {
  alert("hello");
};

var b = new a();
b.sayHello();
```

> 就如之前我们常说的 ECMAScript 中的很多引用类型都重写了自身的 `toString`、`toLocaleString`、`valueOf` 等方法一样，这些引用类型虽然通过原型链(**proto**) 继承了 Object 上的 `toString`、`toLocaleString`、`valueOf` 方法，但是为了自身的特殊情况考量，也对自己的 prototype 属性上进行了对应方法的重写，以让自己的实例对象继承调用。

**caller**

当函数存在嵌套调用时，返回被嵌套函数父函数的引用。

**call 与 apply**
前面我们已经知道，作为函数并在全局环境中执行，this 默认指向的是 `window`。而在严格模式下则为 `null` 。如果作为方法，则 this 指向的是调用这个方法的对象本身。

函数对象自带的 `call` 与 `apply` 方法则可以自定义函数或方法内部的 this 指向。

```javascript
var params = { color: "Red" };

function sayColor(r, g, b) {
  alert(this.color + ":" + r + "," + g + "," + b);
}

function say(r, g, b) {
  sayColor.apply(params, arguments);
}

sayColor.call(params, 255, 255, 255);
say(255, 255, 255);
```

从示例中可以看出，`call` 与 `apply` 在功能上是相同的，主要区别是接收的参数形式不同，call 方法依次接收，而 apply 方法则可以接收一个参数数组。

因此在具体的使用中是选择 `call` 还是 `apply` 最主要的抉择还是调用时传参的形式。

**bind**
`bind` 方法是 ES5 标准新加入的方法，兼容性上 IE9+ 、FF4+、Safari 5.1+。

`bind` 方法也可以改变一个函数或方法内部的 this 指向，在使用格式上也与 `call` 方法完全相同。

`bind` 方法与 `call` 或者是 `apply` 等方法最大的区别就是 `bind` 方法在改变 this 指向后并不会立即调用函数而是返回一个新的函数。

```javascript
var params = { color: "Red" };

function say(r, g, b) {
  alert(this.color + ":" + r + "," + g + "," + b);
}

var sayColor = say.bind(params, 255, 255, 255);

sayColor();
```

> `call` 、`apply` 、 `bind` 等方法的第一个参数就是函数或方法内部 this 所要指向的对象。其它的参数与普通函数或方法的形参相同。

## 函数的转换方法

`Function` 类型也在自己的 `prototype` 上重写了适合自身的 `toString`、`toLocaleString`、`valueOf`等方法。

`toString`、`toLocaleString`　在不同的浏览器，不同的平台其返回值不尽相同。而 `valueOf` 则通常返回函数实例对象自身。

```javascript
function a() {}

a.toString(); //"function a(){}"
a.toLocaleString(); //"function a(){}"
a.valueOf(); //ƒ a(){}
```

## 代码执行原理

在简单学过“执行上下文”，“作用域”、“作用域链”、“变量提升”、“函数（arguments\this）”等基础知识后，现在我们尝试把这些零碎的知识点串联起来，去整体的理解和学习。

JavaScript 中可执行的代码(Execution Code)分为三种：“全局代码”、“函数代码”，“Eval 代码”。

- 全局代码：全局作用域下的 JS 代码，通常可以理解为附加在 `window` 对象上的代码。
- 函数代码：函数中的代码，具有封闭独立的局部作用域。
- Eval 代码：ES5 已不建议使用。

JS 引擎在执行这些代码时会首先创建对应类型的执行上下文（Execution Context，EC）即：“全局执行上下文”、“函数执行上下文”。每个执行上下文都可以抽象理解为具有较为封闭的空间或容器，因此在一个 JS 程序中必然有且只有一个的执行上下文便是“全局执行上下文”，而其它的大多数都是“函数执行上下文”。

在 JS 中用来管理“执行上下文”的是 -- “执行上下文栈(Execution context stack，ECS)”。“执行上下文栈”类似于 `Array` 类型中的“栈”结构。每当 JavaScript 引擎在创建一个执行上下文时，便会将其依次 `push` 到执行栈中，而作为最早创建的执行上下文 - “全局执行上下文”必然在“执行栈”的最底部。

```javascript
var ECStack = [];

// 首先加载“全局执行上下文”

ECStack.push("<globalContext>");

// 随着代码的执行，其它的执行上下文也会被依次加入到这个 执行栈中。

ECStack.push("<fun1>,functionContext");
ECStack.push("<fun2>,functionContext");
ECStack.push("<fun3>,functionContext");
```

理解了“执行栈”与“执行上下文”的关系后，我们可以想象出 JS 程序的执行，就像是在一条流水线上源源不断的将包装盒传送到对应的机器中进行拆解处理，每个包装盒的拆解都类似于一个“执行上下文”的执行。当“执行栈”中的某个执行上下文执行完毕后，便会将其从“执行栈”中弹出，其内部的代码、资源、标识符也统统会被销毁。所以 JavaScript 代码不仅仅是一行一行的顺序执行，更严格的来说是一块一块（执行上下文）的顺序执行。

如上所述“执行上下文”我们可以将其抽象理解成一个包装盒，每个包装盒的内部都存放着两个非常重要的物品（对象）：

- 变量对象(VO)/活动对象(AO)
- 作用域链(scope chain)

当 JS 引擎进入到执行上下文的内部并开始执行代码时，会首先向该上下文中加载代码与所需的数据，然后将需要声明的函数与变量放在源代码树的顶部，进行预先声明，声明后的变量与函数会被加入到“变量对象(VO)” 中进行管理。

“变量对象(VO)” 的结构是一个 `Map` 结构。

```javascript
var a = 1;
var b = 2;

function c(){}

//---------------------------

var EC_GlobalContext = {
    VO:{
        a:1,
        b:2,
        c:'reference to function c(){}',
        alert:'ƒ alert() { [native code] }',
        ...
        this:global{//...},
    },
    scopeChain:null
}

//---------------------------
```

在全局执行上下文中，我们可以认为“变量对象(VO)” 就是 `window` 对象，也就是说在“全局上下文”中变量对象是可访问，可赋值的 。但在“函数执行上下文”中，“变量对象(VO)” 将无法被直接访问，而且在“函数执行上下文”中变量对象相比“全局执行上下文”又增加了 `arguments` 对象，此时的变量对象也被称之为“活动对象(AO)”。

```javascript
var AO = {
  //...
  arguments: {
    0: "[value1]",
    1: "[value2]",
    //....,
    length: "[number]",
    callee: "<function,self>"
  },
  this: "reference to window"
  //....
};
```

每个“执行上下文”都有属于自己的独立空间，因此具有关联的多个执行上下文，会通过“作用域链(scope chain)”来进行关联，每个执行上下文内的的标识符解析都会率先从自身的“变量对象”中查找，如果查找不到，则沿着作用域链的关联路径继续向上解析，一直到达“全局执行上下文”，因此我们所说的容器与封闭空间的概念，实际上就是我们常说的 —— “作用域”。

> 或者说作用域的范围实则是作用域链的最大延伸范围。

在执行上下文中，作用域链的关联关系是通过多个“执行上下文”是否存在嵌套关系来确定的，例如在 JS 中所有局部代码都嵌套在全局代码中，因此所有局部执行上下的作用域链的最顶层都会是“全局执行上下文”，这也说明为什么我们不论在哪里书写局部代码，总可以访问到 `window` 对象上的方法或属性。为什么是嵌套而不是其它呢？原因是 JS 这门语言属于词法作用域（lexcial scope） 而非动态作用域(Dynamic scope)，其作用域的访问只在乎代码在哪里书写，而非在哪里执行。

看下面的代码：

```javascript
function fn1() {
  var chain2 = "lexical";

  function fn3() {
    console.log(chain2);
  }

  fn3();

  console.log(chain1);
}

function fn2() {
  var chain1 = "dynamic";
  fn1();
}

fn2();
```
