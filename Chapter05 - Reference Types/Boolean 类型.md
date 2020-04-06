# 《JavaScript 高级程序设计》第五章：Boolean 类型

## 简介

"Boolean" 类型，即“布尔类型”，值为“布尔值”，布尔值有两种：`true` 或 `false`。

"Boolean" 类型创建方式有两种，一种是通过构造函数来创建其“引用类型”的 Boolean 值。

```javascript
var booleanObject = new Boolean(NaN); //Boolean {false}

typeof booleanObject; //"object"
booleanObject instanceof Boolean; //true
```

另一种则是使用字面量格式，创建“基本类型”的 Boolean 值。

```javascript
var boolean = true; //true

typeof boolean; //“boolean”;
boolean instanceof Boolean; //false
```

> Boolean 值有两种，一种是作为“基本类型”的布尔值，另一种是作为“引用类型”的布尔值，对于这样的类型我们可以称之为“基本包装类型”。

## Boolean()

`Boolean()`   函数既是“构造函数”也可以是一个“转换方法”。

作为“构造函数”它会把传入的参数按照一定条件转换为对应的布尔值并返回一个 Boolean 类型的实例对象，而作为“转换方法”则只会根据条件将参数转换成对应的“基本类型”布尔值然后返回。

```javascript
var booleanObject = new Boolean(0); //Boolean {false}
var boolean = Boolean(null); //false
```

## 转换方法

同样的 `Boolean` 类型也重写了自身的 `toString()`、`toLocaleString()`、`valueOf()` 等方法，用于让自己的实例对象来继承。

```javascript
var booleanObject = new Boolean(true);
booleanObject.toString(); //"true"
booleanObject.toLocaleString(); //"true"

booleanObject.valueOf(); //true
```

`toString()` 与 `toLocaleString()` 都会以字符串的形式返回其对应的 布尔值，而 `valueOf()` 方法则返回对应布尔类型的布尔值。

## 问题与影响

我们的建议是最好永远不要使用 `Boolean` 的引用类型，因为这会很容易对我们在进行真假判断时产生误解：

```javascript
var condition = new Boolean(0);

if (condition) {
  //do something...
}
```

也或者在类型判断上产生歧义：

```javascript
var boolean = true;
var booleanObject = new Boolean(true);

typeof boolean; //"Boolean"
typeof booleanObject; //"Object"
```
