# 《JavaScript 高级程序设计》第五章：Number 类型

## 简介

Number 对象是 基本类型数字值 的引用类型。

Number 类型与 Boolean 类型相同都属于“基本包装类型”。

```javascript
var num = new Number(10); //Number的引用类型
var count = 1; //Number的基本类型

num.toString(); // 10;
count.toString(); // 1;
```

## 转换方法

与其它 “引用类型” 相同，Number 类型也重写了 `toString()`、`toLocaleString()`、`valueOf()` 等方法。

其中 `toString()` 与 `toLocaleString()` 返回数值的字符串形式，而 `valueOf()` 则返回数值对象对应的基本类型数值。

```javascript
typeof num; //object
typeof num.valueOf(); //number
```

需要注意的是，Number 对象重写后的 `toStrng()` 有一个非常有用的功能，那就是接收一个“基数”作为参数，然后以字符串形式返回对应进制的转换结果。

```javascript
num.toString(); //"10"
num.toString(10); //"10"
num.toString(8); // "12"
num.toString(2); // "1010"
num.toString(16); // "a"
```

## Number()

`Number()` 既是“构造函数”也可以是一个普通的函数(将构造函数充当普通函数调用)。其功能是将其它类型的数据转换为对应的“数值”类型。

作为“构造函数”它会把传入的参数按照 ECMA 规范转换为对应的数值并返回一个 `Number` 类型的实例对象，而作为“转换方法”则只会根据规范将参数转换为对应“基本类型”的数值然后返回。

```javascript
var numberObject = new Number(null); //Number {0}
var number = Number(false); //0
```

> 关于 `Number()` 转换方法将其它类型数据转换为数值的具体规则，请参考第三章。

## 实例方法

Number 对象的实例方法是其实例对象继承自构造函数 `Number()` 原型对象上的方法。

### toFixed()

以指定的小数位数返回数值的字符串形式，会发生四舍五入。

```javascript
var num = new Number(10.1236);
num.toFixed(); //"10"
num.toFixed(0); //"10"
num.toFixed(1); //"10.1"
num.toFixed(3); //"10.124"
```

> 缺省参数时，默认为 `0`，类似于取整操作。如果存在小数位数被舍去的情况，它终是以被舍去的那位进行四舍五入。

### toExponential()

以字符串的形式返回数值的科学计数法，格式为：`MeR`，其中 M 为尾数，e 默认为 10，R 则是指数。

```javascript
(10).toExponential(); //"1e+1"
(99).toExponential(); //"9.9e+1"
(100).toExponential(); //"1e+2"
```

另外，`toExponential()` 方法也接收一个参数用来返回尾数的小数尾数。

```javascript
(99).toExponential(); //"9.9e+1"
(99).toExponential(1); //"9.9e+1"
(99).toExponential(2); //"9.90e+1"
(99).toExponential(3); //"9.900e+1"
```

### toPrecision()

`toPrecision()` 方法会根据数值的特点自动调用 `toFixed()` 方法或 `toExponential()` 方法返回适合的数值格式。

`toPrecision()` 方法也接收一个参数，用于控制返回值的位数（不包括指数位数）。

```javascript
(99).toPrecision(); //"99"
(99).toPrecision(1); //"1e+2";
(99).toPrecision(2); //"99"
(99).toPrecision(3); //"99.0"
```

当参数为空或正好为数值的长度（2）也或者大于数值的长度，此时 `toPrecision` 会调用 `toFixed()` 方法，而当参数小于数值的长度（1）时，则会调用 `toExponential()` 方法，但是 `99` 的位数还是大于 1，所以只好舍去，然后进位，最后结果即：`1e+2`。

## 问题

受限于 JavaScript 存储浮点数位数限制，在进行小数运算时会出现精度丢失的问题。

```javascript
0.1 + 0.2; //0.30000000000000004
0.1 + 0.7; //0.7999999999999999
```

解决这个问题，我们可以引入一个现成的数学库 — `math.js`。

```javascript
math.config({
  number: "BigNumber"
});

math.parser().eval(0.1 + "+" + 0.2);
```
