# 《JavaScript 高级程序设计》第五章： RegExp 类型

## 创建 RegExp 表达式

在 ECMAScript 中创建正则表达式的方式有两种：

- 字面量
- RegExp 构造函数

```javascript
var expression = /pattern/modifier
var expression = new RegExp("pattern","modifier");
```

一个完整的正则表达式主要有两部分组成：“匹配模式(pattern)”与“修饰符(modifier)”。

“匹配模式”是正则表达式的主体”，它定义了具体的匹配规则，而“修饰符”则用于更进一步说明规则的范围和细节：

- `g` 表示全局匹配。
- `i` 表示区分大小写。
- `m` 表示多行匹配。

再详细一些，“匹配模式”本身又由“普通字符”与“元字符”两部分组成。

“普通字符”：我们可以理解为计算机语言中的常量字符，它包括可见字符，比如 a-zA-Z,0-9 这些与另一些不可见字符，例如，ASCII 码值在 0-31 的“控制字符”，虽然此类字符不可显示打印，但确实存在，它们通常用于特殊功能的代理。

“元字符”：则类似于我们接触到的“变量”，它们在正则表达式中具有特殊的含义与功能，例如字符类、限定符、分组、向前查找以及反向引用等。常见的元字符有：`( [ { \ ^ $ | ) ? * + .]}`。可以说正则表达式强大的字符匹配功能就是通过对这些“元字符”的按需组合使用。

但是如果我们要匹配的内容中本身就含有元字符，那么在匹配的时候必须要对元字符进行转义，而这个转义功能则由另一个元字符 `\` 实现。

```javascript
\\b => '\b'
\[ => '['
\. => '.'
```

如果是通过构造函数实例化的正则表达式对象，由于匹配模式与修饰字符都是字符串形式的参数，所以在转义的时候，要进行双重转义。

```javascript
new RegExp("\\[abc\\]", "ig");
```

这是因为在字符串中 `\` 反斜杠本身就具有转义功能，所以如果仍然按照字面量那种单层转义，得到的仍然是元字符本身。例如：

```
"\." => "." => new RegExp("\.") => /./
```

这些都是很简单易懂的，稍微有点麻烦的就是要转义本身就含有斜杠符号的元字符，例如 `\b`、`\d` 等。对于这样的特殊符号，我们先从转义 斜杠 `\` 本身开始理解。

```javascript
/\\/ => "\\"
```

要注意的是斜杠 `\` 在字符串中本身就具有转义功能，所以不可能单独存在 ""。否则会抛出语法异常的，那么下面 `\b`、`\d` 就很好理解了。

```javascript
/\\b/ => "\\b" (我们形象上认为的 "\b"形式)。
/\\d/ => "\\d"  (我们形象上认为的 "\d"形式)。

new RegExp("\\\b") => /\\b/
new RegExp("\\\d") => /\\d/
```

## 实例属性

RegExp 实例对象（字面量对象）所具有的属性。

- global：布尔值，表示是否设置了 g 标志。
- ignoreCase：布尔值，表示是否设置了 i 标志。
- multiline：布尔值，表示是否设置了 m 标志。
- lastIndex：数值，下次匹配的起始位置，默认值为 0.
- source：字符串，以字符串的形式返回匹配模式本身。

## 转换方法

RegExp 实例对象（字面量对象）从 `RegExp` 的原型上所继承的一些转换方法。

与大多数其它引用对象一样，RegExp 类型也重写了 toString，toLocaleSting，valueOf 等方法，使他们的功能与返回值更切合自身。

在 RegExp 类型中，`toString()` 与 `toLocaleString()` 方法会以字符串的形式返回正则实例对象的表达式，而 `valueOf()` 则会返回正则表达式本身，即一个对象的类型。

```javascript
var pattern = /cat/gi;

pattern.toString(); // '/cat/gi' | string
pattern.toLocaleString(); // '/cat/gi' | string
pattern.valueOf(); // /cat/gi | object
```

## 实例方法

RegExp 实例对象（字面量对象）从 `RegExp` 的原型上所继承的方法。

### test

功能：根据“匹配模式”在要匹配的字符串中是否具有符合匹配条件的结果，来返回一个布尔值。

返回值：Boolean

格式：`pattern.test(text);`

说明：如果正则表达式中含有全局(g)的修饰符，并进行了多次匹配操作，则 `test` 会根据 `lastIndex` 的值作为锚点继续对要匹配的字符串按位置索引继续向下查找匹配。

```javascript
var pattern = /a/g;
var text = "ababab";

pattern.test(text);
console.log(pattern.lastIndex);
pattern.test(text);
console.log(pattern.lastIndex);
```

当然 test 常常结合 if 语句进行条件是否成立的判断，所以只需要判断一次即可。

### exec

功能：根据正则表达式的匹配结果来返回一个 Like Array 或者是 null 的结果。

返回值：Array | null

格式：`pattern.exec(text);`

说明：如果表达式在所要匹配的字符串中有符合条件的匹配结果，则返回一个 Link Array，这个 Array 的结构如下：

```javascript
[result,group1,group2,...,input:text,index:number]
```

- result:匹配的结果字符串。
- group1，group2...：如果有分组匹配的话，则表示每个分组的值。
- input:返回原字符串
- index:返回匹配结果首字符在原字符串中的索引位置。

```javascript
var text = "ababab";
var pattern1 = /(a)/;
var pattern2 = /.(b)/;
console.log(pattern1.exec(text)); //(2) ["a", "a", index: 0, input: "ababab"]
console.log(pattern2.exec(text)); //(2) ["ab", "b", index: 0, input: "ababab"]
```

与 `test` 方法相同在非全局模式下，一旦匹配到符合规则的结果，便会停止执行，就算是多次执行，返回都是第一次匹配的结果。

```javascript
var text = "ababab";
var pattern = /a/g;

console.log(pattern.exec(text)); //["a", index: 0, input: "ababab", groups: undefined]
console.log(pattern.exec(text)); //["a", index: 0, input: "ababab", groups: undefined]
console.log(pattern.exec(text)); //["a", index: 0, input: "ababab", groups: undefined]
```

若指定了全局修饰符，那么则会根据正则对象实例的　`lastIndex` 属性作为锚点作为起点继续对原字符串进行向下匹配。此时每次的匹配结果都会不同。

```javascript
var text = "ababab";
var pattern = /a/g;

console.log(pattern.exec(text)); //["a", index: 0, input: "ababab", groups: undefined]
console.log(pattern.exec(text)); //["a", index: 2, input: "ababab", groups: undefined]
console.log(pattern.exec(text)); //["a", index: 4, input: "ababab", groups: undefined]
console.log(pattern.exec(text)); //null
console.log(pattern.exec(text)); //["a", index: 0, input: "ababab", groups: undefined]
```

> 当匹配到字符串的末尾并返回 `null` 的时候，则会重新进行匹配。

通过一个循环收集每个匹配结果的索引位置来深入了解全局模式下的 `exec`方法。

```javascript
var text = "ababab";
var pattern = /a/g;
var indexs = [];
var result;

while ((result = pattern.exec(text))) {
  indexs.push({ index: result.index, lastIndex: pattern.lastIndex });
}
```

### compile

功能：对原有的正则对象实例重新编译，包括重置匹配模式与修饰符。

返回值：正则实例对象本身。

```javascript
var pattern = /a/g;
pattern.compile("b", "i");
```

> 说明：一般来说该方法基本不常用，通过对正则实例对象重新赋值也可以达到同等效果。

## RegExp 静态属性

这些属性都是 `RegExp` 构造函数的静态属性，它们的值受当前作用域正在应用的正则实例对象所影响。

### input

作用域中当前正则对象实例所要匹配的原字符串。

```javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;

pattern.test(text);
console.log(RegExp.input); //this has been a short summer
```

### lastMatch

作用域中当前正则实例最近一次匹配结果。

```javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;

pattern.test(text);
console.log(RegExp.lastMatch); // short
```

### lastParen

作用域中当前正则实例对象最近一次分组的匹配结果。

```javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;

pattern.test(text);
console.log(RegExp.lastParen); // s
```

### leftContext

以作用域中当前正则实例对象的匹配结果为上下文，返回之前的子串

```javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;

pattern.exec(text);
console.log(RegExp.leftContext); //this has been a
```

### rightContext

以作用域中当前正则实例对象的匹配结果为上下文，返回之后的子串

```javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;

pattern.exec(text);
console.log(RegExp.rightContext); // summer
```

### $1~$9

返回作用域中当前正则实例对象最近一次匹配的 1-9 个分组结果。

```javascript
var text = "chart 100";
var pattern = /(\w+) (\d+)/;
pattern.test(text);
console.log(RegExp.$1);
console.log(RegExp.$2);
```
