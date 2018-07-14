# 正则的扩展

1. [RegExp构造函数](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-正则的扩展.md#RegExp构造函数)
2. [字符串的正则方法](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-正则的扩展.md#字符串的正则方法)
3. [u修饰符](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-正则的扩展.md#u修饰符)
4. [RegExp.prototype.unicode属性](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-正则的扩展.md#RegExp.prototype.unicode属性)

------

## RegExp构造函数

在ES5中，`RegExp`构造函数的参数有两种情况

在第一种情况是，参数是字符串，这是第二份参数表示正则表达式的修饰符（flag）

```
var regex = new RegExp('xyz','i');
// 等价于
var regex = /xyz/i;
```

第二种情况是，参数是一个正则表达式，这时会返回一个原有正则表达式的拷贝

```
var regex = new RegExp(/xyz/i/);
//等价于
var regex = /xyz/i;
```

但是，ES5不允许使用第二个参数添加修饰符，否则报错

```
var regex = new RegExp(/xyz/,'i');
// Uncaught TypeError: Cannot supply flags when constructing one RegExp from another
```

ES6 改变了这种行为，如果`RegExp`构造函数第一个参数是一个正则对象，那么可以使用第二个参数指定修饰符。而且，返回的正则表达式会忽略原有的正则表达式的修饰符，只使用新指定的修饰符

```
new RegExp(/abc/ig).flags
// i
```

上面代码中，原有正则对象的修饰符是`ig`，它会被第二个参数`i`覆盖。 

------

## 字符串的正则方法

字符串对象共有4个方法，可以使用正则表达式： `match()` `replace()` `search()` 和`split()`

ES6将这4个方法，在语言内部全部调用`RegExp`的实例方法，从而做到所有与正则相关的方法，全都定义在`RegExp`对象上

- `String.prototype.match` 调用 `RegExp.prototype[Symbol.match]`
- `String.prototype.replace` 调用 `RegExp.prototype[Symbol.replace]`
- `String.prototype.search` 调用 `RegExp.prototype[Symbol.search]`
- `String.prototype.split` 调用 `RegExp.prototype[Symbol.split]`

------

## u修饰符

ES6 对正则表达式添加了`u`修饰符，含义为“Unicode 模式”，用来正确处理大于`\uFFFF`的 Unicode 字符。也就是说，会正确处理四个字节的 UTF-16 编码。

```
/^\uD83D/u.test('\uD83D\uDC2A') // false
/^\uD83D/.test('\uD83D\uDC2A') // true
```

上面代码中，`\uD83D\uDC2A`是一个四个字节的 UTF-16 编码，代表一个字符。但是，ES5 不支持四个字节的 UTF-16 编码，会将其识别为两个字符，导致第二行代码结果为`true`。加了`u`修饰符以后，ES6 就会识别其为一个字符，所以第一行代码结果为`false`。

一旦加上`u`修饰符号，就会修改下面这些正则表达式的行为。

**（1）点字符**

点（`.`）字符在正则表达式中，含义是除了换行符以外的任意单个字符。对于码点大于`0xFFFF`的 Unicode 字符，点字符不能识别，必须加上`u`修饰符。

```
var s = '𠮷';

/^.$/.test(s) // false
/^.$/u.test(s) // true
```

上面代码表示，如果不添加`u`修饰符，正则表达式就会认为字符串为两个字符，从而匹配失败。

**（2）Unicode 字符表示法**

ES6 新增了使用大括号表示 Unicode 字符，这种表示法在正则表达式中必须加上`u`修饰符，才能识别当中的大括号，否则会被解读为量词。

```
/\u{61}/.test('a') // false
/\u{61}/u.test('a') // true
/\u{20BB7}/u.test('𠮷') // true
```

上面代码表示，如果不加`u`修饰符，正则表达式无法识别`\u{61}`这种表示法，只会认为这匹配 61 个连续的`u`。

**（3）量词**

使用`u`修饰符后，所有量词都会正确识别码点大于`0xFFFF`的 Unicode 字符。

```
/a{2}/.test('aa') // true
/a{2}/u.test('aa') // true
/𠮷{2}/.test('𠮷𠮷') // false
/𠮷{2}/u.test('𠮷𠮷') // true
```

**（4）预定义模式**

`u`修饰符也影响到预定义模式，能否正确识别码点大于`0xFFFF`的 Unicode 字符。

```
/^\S$/.test('𠮷') // false
/^\S$/u.test('𠮷') // true
```

上面代码的`\S`是预定义模式，匹配所有非空白字符。只有加了`u`修饰符，它才能正确匹配码点大于`0xFFFF`的 Unicode 字符。

利用这一点，可以写出一个正确返回字符串长度的函数。

```
function codePointLength(text) {
  var result = text.match(/[\s\S]/gu);
  return result ? result.length : 0;
}

var s = '𠮷𠮷';

s.length // 4
codePointLength(s) // 2
```

**（5）i 修饰符**

有些 Unicode 字符的编码不同，但是字型很相近，比如，`\u004B`与`\u212A`都是大写的`K`。

```
/[a-z]/i.test('\u212A') // false
/[a-z]/iu.test('\u212A') // true
```

上面代码中，不加`u`修饰符，就无法识别非规范的`K`字符。

------

## RegExp.prototype.unicode属性

正则实例对象新增`unicode`属性，表示是否设置了`u`修饰符。

```
const r1 = /hello/;
const r2 = /hello/u;

r1.unicode // false
r2.unicode // true
```

上面代码中，正则表达式是否设置了`u`修饰符，可以从`unicode`属性看出来。

------

