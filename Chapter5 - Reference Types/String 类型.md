# 《JavaScript 高级程序设计》第五章：String 类型

## 简介

`String` 对象是对应基本类型“字符串”的引用类型。

`String` 类型与 `Number`、`Boolean` 类型都属于“基本包装类型”。

`String` 类型也具有数组的某些特征，例如 `length` 属性，可以返回字符串中字符的个数。

```javascript
var sayHello = new String("hello world");
sayHello.length; //11
```

> PS: `String` 的 `length` 属性只能统计字符串中的字符个数，但是并不能区分该字符是单字节字符还是多字节字符。

```javascript
"您好，世界！".length; //6
```

`String` 对象与数组一样，也支持通过下标来访问某个字符：

```javascript
sayHello[0]; //'h'
sayHello[1]; //'e'
```

## 转换方法

`String` 对象也具有 `toString()`、`toLocaleString()`、`valueOf()` 等转换方法，它们都返回 `String` 对象对应的基本类型字符串值。

```javascript
var str = new String("String");

typeof str; //"object"
typeof str.toString(); //"string"
typeof str.toLocaleString(); //"string"
typeof str.valueOf(); //"string"
```

## String()

`String()` 既是“构造函数”也可以是一个普通的函数(将构造函数充当普通函数调用)。其功能是将其它类型的数据转换为对应的“字符”型结果。

作为“构造函数”它会把传入的参数按照 ECMA 规范转换为对应的字符串并返回一个 `String` 类型的实例对象，而作为“转换方法”则只会根据规范将参数转换为对应“基本类型”的字符串然后返回。

```javascript
var stringObject = new String(undefined); //String {"undefined"}
var string = String(null); //"null"
```

> 关于 `String()` 转换方法将其它类型数据转换为字符串的具体规则，请参考第三章。

## 实例方法

“实例方法” 指的是 `String` 对象构造函数原型(prototype)对象上定义的方法，可以被其实例对象所继承。

### 字符与编码

- charAt() / charCodeAt()

`charAt` 与 `charCodeAt` 方法都接收一个位置（数值）作为参数，然后以单字节的形式返回字符串中指定位置的字符。它们的区别在于前者返回的是字符本身，而后者返回的是字符所对应的编码（十进制形式）。

```javascript
var str = new String("string");
var name = "我";

str.charAt(0); //s
str.charCodeAt(1); //116

name.charAt(); //我
name.charCodeAt(); //25105
```

### 字符串操作

- concat()

`concat()` 方法可以接收多个字符串参数，并将所有参数都拼接成一个完整的字符串。

```javascript
var str = new String("h");
str.concat("e", "l", "l", "o");
```

- toLowerCase() / toUpperCase()

字符串的大小写转换。`toLowerCase` 转换为小写，`toUpperCase` 转换为大写。

- trim()

`trim` 方法是 ES5 新增的方法。

`trim` 方法用于清除两端空格。

```javascript
var str = new String(" a b c ");
str.trim(); //'a b c'
```

### 字符串截取

- slice() / substr() / substring()

这三个方法都会从原字符串的指定范围内截取并返回一个子串。

它们都接收一到两个参数，第一个参数是截取的起始位置，而第二个参数则是截取的结束位置（不包含最后一个字符），如果第二个参数缺省，则默认为截取到整个字符串的尾部。

```javascript
var str = "hello world";

str.slice(0, 1); //"h"
str.slice(3); //"lo world"
str.substring(0, 1); //"h"
str.substring(3); //"lo world"
```

另外 `slice` 方法还可以接受负数作为参数，如果为负数，那么真正的值则是整个字符串的长度 `length` 加上这个负数。

```javascript
var str = "abcd";

str.length; //4
str.slice(-1); //d => str.slice(-1+4=3)
str.slice(-2, -1); //c => str.slice(-2+4=2,-1+4=3);
```

而第二个参数对于 `substr` 方法要较为特殊，此时不再是截取的结束位置，而是要截取的字符数量。

```javascript
str.substr(3, 2); //"lo"
str.substr(3); //"lo world"
```

> 如果缺省 `substr` 方法的最后一个参数，默认也是截取到最后一个字符。

### 字符串位置

- lastIndexOf() / indexOf()

`lastIndexOf` 方法与 `indexOf` 方法都可以从一个字符串中查找给定的字符或子串，如果查找到，则返回这个字符或子串在原字符串中的(首字符)索引位置，否则返回 `-1`。它们的区别则是前者从字符串开头位置向后查找，而后者则是完全相反的方向。

```javascript
var str = new String("hellow world");
str.indexOf("e"); //1;
```

另外，这两个方法还可以接受第二个参数，用来指定查找的起始位置。

```javascript
str.indexOf("e", 2); //-1
str.lastIndexOf("e", 0); //-1
```

利用字符串的位置方法，我们可以从一段文本中筛选出指定字符或字串出现的次数与位置。

```javascript
var string =
  "Good love makes you see the whole world from one person while bad love makes you abandon the whole world for one person.";
var findChar = "love";
var posArr = [];
var pos = string.indexOf(findChar);

while (pos != -1) {
  posArr.push(pos);
  pos = string.indexOf(findChar, pos + 1);
}
```

### 字符串模式匹配方法

> 字符串模式匹配方法可以与正则表达式结合使用。

- search()

`search()` 方法与 `indexOf()` 方法非常相同，它们都会在一个字符串中查找给定的字符或子串，并返回位置值，如果没有查找到则返回 `-1`。

```javascript
var str = "hello";
str.search("h"); //0
```

区别则是 `search()` 方法只接受一个参数，既要查找的字符串或者是一个正则表达式对象。

```javascript
str.search(/e/); //1
```

- match()

`match()` 方法的功能与返回值格式非常类似于 `RegExp` 对象中的 `exec()` 方法。

`match(pattern)` 方法只接受一个参数，即要匹配的模式，这个匹配模式可以是一个普通的字符或子串，也可以是一个正则表达式对象。

```javascript
var str = "hellow";
str.match(/(l)/); // ["l", "l", index: 2, input: "hellow"]
str.match(/(k)/); // null
```

`match()` 方法的返回值是一个数组，数组中的第一个元素是匹配到的结果，而之后的数组元素则是每个分组匹配到的结果，`index` 的值表示匹配结果的首字符所在原字符串中的位置索引，而 `input` 则是返回原字符串本身，若没有匹配到任何结果，则返回 `null`。

总的来说 `match()` 返回值主要包括：“匹配结果”、“分组结果”、“结果索引”、“原字符串”。

- replace()

`replace()` 方法实现了字符串的替换操作，它接收两个参数，分别是“匹配模式”以及要替换的内容。“匹配模式”可以是一个字符串，也可以是一个正则对象。要替换的内容可以是一个字符串也可以是一个回调函数，然后由这个回调函数来返回要替换的内容。

```javascript
var str = "hellow";
str.replace("ellow", "i"); //"hi"
str.replace(/e.+/, "i"); //"hi"
```

如果第二个参数（要替换的内容）是一个字符串，则还可以使用一些特殊的字符序列，将正则表达式的操作结果插入到结果字符串中。

```javascript
str.replace(/(e.+)/, "(i|$1)"); //"h(i|ellow)"
```

|                              字符序列                               |                           替换文本                           |
| :-----------------------------------------------------------------: | :----------------------------------------------------------: |
|                                \$\$                                 |                              \$                              |
|                                 \$&                                 |     匹配整个模式的子字符串。与 RegExp.lastMatch 的值相同     |
|                                 \$'                                 | 匹配的子字符串之前的子字符串。与 RegExp.leftContext 的值相同 |
| \$` | 匹配的子字符串之后的子字符串。与 RegExp.rightContext 的值相同 |
|                               \$n[m]                                |                      指定分组的匹配结果                      |

> 关于这些“字符序列”我们可以等同理解为 `RegExp` 对象的静态属性，它作用在当前正则实例的执行上下文中。

如果第二个参数是一个回调函数，那么这个函数可以接受三个参数，分别是匹配结果、结果首字符的索引位置以及原字符串。如果匹配模式是一个正则对象，且指定了全局修饰符 `g`，那么回调函数便会根据匹配结果的次数被多次调用。

```javascript
function htmlEscape(text) {
  return text.replace(/[<>"&]/g, function(result, resultIndex, originalText) {
    switch (result) {
      case "<":
        return "&lt;";
      case ">":
        return "&gt;";
      case '"':
        return "&quot;";
      case "&":
        return "&amp;";
    }
  });
}
htmlEscape(String().big()); //"&lt;big&gt;&lt;/big&gt;"
```

- split()

`split()` 接收一个“分隔符”作为参数，将原字符串分解成多个子串，然后保存在一个数组中。

```javascript
var str = "red,orange,yellow,green";
str.split(","); //(4) ["red", "orange", "yellow", "green"]
```

另外，这个“分隔符”还可以是一个正则对象，从而让 `split()` 方法根据正则的匹配结果来分解原字符串。

```javascript
str.split(/[^\,]+/); //(5) ["", ",", ",", ",", ""]
```

### 字符串比较

- localeCompare()

`localeCompare` 方法会按照本地区的特殊性进行字符串的大小比较。

如果字符串对象大于接收的字符串参数，则返回 `1` 。

如果字符串对象小于接收的字符串参数，则返回 `-1`。

如果两个字符串完全相同，则返回 `0`。

```javascript
var str1 = "abc";
var str2 = "ghi";
var string = "def";

string.localeCompare(str1); //1
string.localeCompare("def"); //0
string.localeCompare(str2); //-1
```

### HTML 方法

String 对象中提供了很多快速创建 HTML 字符串的方法。

```javascript
var s = new String("Html String Test");

s.big(); //"<big>Html String Test</big>"
```

|     方法     |           输出结果           |
| :----------: | :--------------------------: |
| anchor(name) | `<a name= "name">string</a>` |
|    big()     |     `<big>string</big>`      |
|    bold()    |       `<b>string</b>`        |
|   fixed()    |      `<tt>string</tt>`       |
|  italics()   |       `<i>string</i>`        |
|  link(url)   |  `<a href="url">string</a>`  |
|   small()    |   `<small>string</small>`    |
|   strike()   |  `<strike>string</strike>`   |
|    sub()     |     `<sub>string</sub>`      |
|    sup()     |     `<sup>string</sup>`      |

静态方法

- fromCharCode()

`fromCharCode` 是 `String` 对象的静态方法。接收一个或多个十进制的字符编码作为参数并返回这些编码对应的字符本身。

从功能上看 `fromCharCode` 方法与 String 实例对象的 `charCodeAt` 方法是相反的。

```javascript
String.fromCharCode(
  "h".charCodeAt(),
  "e".charCodeAt(),
  "l".charCodeAt(),
  "l".charCodeAt(),
  "o".charCodeAt()
);
String.fromCharCode(0x2300); //"⌀"
```
