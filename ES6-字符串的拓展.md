# 字符串的扩展

1. [字符的 Unicode 表示法](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#字符的 Unicode 表示法)
2. [codePointAt()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#codePointAt())
3. [String.fromCodePoint()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#String.fromCodePoint())
4. [字符串的遍历器接口](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#字符串的遍历器接口)
5. [at()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#at())
6. [normalize()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#normalize())
7. [includes(), startsWith(), endsWith()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#includes(),%20startsWith(),%20endsWith())
8. [repeat()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#repeat())
9. [padStart()，padEnd()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#padStart()%EF%BC%8CpadEnd())
10. [matchAll()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#matchAll())
11. [模板字符串](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#%E6%A8%A1%E6%9D%BF%E5%AD%97%E7%AC%A6%E4%B8%B2)
12. [实例：模板编译](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#%E5%AE%9E%E4%BE%8B%EF%BC%9A%E6%A8%A1%E6%9D%BF%E7%BC%96%E8%AF%91)
13. [标签模板](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#%E6%A0%87%E7%AD%BE%E6%A8%A1%E6%9D%BF)
14. [String.raw()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#String.raw())
15. [模板字符串的限制](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-字符串的扩展.md#%E6%A8%A1%E6%9D%BF%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E9%99%90%E5%88%B6)

ES6 加强了对 Unicode 的支持，并且扩展了字符串对象。

------

## 字符的 Unicode 表示法

JavaScript 允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 Unicode 码点。

```
"\u0061"
// "a"
```

但是，这种表示法只限于码点在`\u000~\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的形式表示

```
"\uD842\uDFB7"
// "𠮷"

"\u20BB7"
// " 7"
```

上面代码表示，如果直接在`\u`后面跟上超过`0xFFFF`的数值（比如`\u20BB7`），JavaScript 会理解成`\u20BB+7`。由于`\u20BB`是一个不可打印字符，所以只会显示一个空格，后面跟着一个`7`。

ES6 对这一点做出了改进，只要将码点放入大括号，就能正确解读该字符。

```
"\u{20BB7}"
// "𠮷"

"\u{41}\u{42}\u{43}"
// "ABC"

let hello = 123;
hell\u{6F} // 123

'\u{1F680}' === '\uD83D\uDE80'
// true
```

上面代码中，最后一个例子表明，大括号表示法与四字节的 UTF-16 编码是等价的。

有了这种表示法之后，JavaScript 共有 6 种方法可以表示一个字符。

```
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```

------

## codePointAt()

JavaScript 内部，字符以 UTF-16 的格式储存，每个字符固定为`2`个字节。对于那些需要`4`个字节储存的字符（Unicode 码点大于`0xFFFF`的字符），JavaScript 会认为它们是两个字符。

```
var s = "𠮷";

s.length // 2
s.charAt(0) // ''
s.charAt(1) // ''
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271
```

上面代码中，汉字“𠮷”（注意，这个字不是“吉祥”的“吉”）的码点是`0x20BB7`，UTF-16 编码为`0xD842 0xDFB7`（十进制为`55362 57271`），需要`4`个字节储存。对于这种`4`个字节的字符，JavaScript 不能正确处理，字符串长度会误判为`2`，而且`charAt`方法无法读取整个字符，`charCodeAt`方法只能分别返回前两个字节和后两个字节的值。

ES6 提供了`codePointAt`方法，能够正确处理 4 个字节储存的字符，返回一个字符的码点。

```
let s = '𠮷a';

s.codePointAt(0) // 134071
s.codePointAt(1) // 57271

s.codePointAt(2) // 97
```

`codePointAt`方法的参数，是字符在字符串中的位置（从 0 开始）。上面代码中，JavaScript 将“𠮷a”视为三个字符，codePointAt 方法在第一个字符上，正确地识别了“𠮷”，返回了它的十进制码点 134071（即十六进制的`20BB7`）。在第二个字符（即“𠮷”的后两个字节）和第三个字符“a”上，`codePointAt`方法的结果与`charCodeAt`方法相同。 

总之，`codePointAt`方法会正确返回 32 位的 UTF-16 字符的码点。对于那些两个字节储存的常规字符，它的返回结果与`charCodeAt`方法相同。

`codePointAt`方法返回的是码点的十进制值，如果想要十六进制的值，可以使用`toString`方法转换一下。

```
let s = '𠮷a';

s.codePointAt(0).toString(16) // "20bb7"
s.codePointAt(2).toString(16) // "61"
```

你可能注意到了，`codePointAt`方法的参数，仍然是不正确的。比如，上面代码中，字符`a`在字符串`s`的正确位置序号应该是 1，但是必须向`codePointAt`方法传入 2。解决这个问题的一个办法是使用`for...of`循环，因为它会正确识别 32 位的 UTF-16 字符。 

```
let s = '𠮷a';
for (let ch of s) {
  console.log(ch.codePointAt(0).toString(16));
}
// 20bb7
// 61
```

`codePointAt`方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。

```
function is32Bit(c) {
  return c.codePointAt(0) > 0xFFFF;
}

is32Bit("𠮷") // true
is32Bit("a") // false
```

------

## String.fromCodePoint()

ES5 提供`String.fromCharCode`方法，用于从码点返回对应字符，但是这个方法不能识别 32 位的 UTF-16 字符（Unicode 编号大于`0xFFFF`）。

```
String.fromCharCode(0x20BB7)
// "ஷ"
```

上面代码中，`String.fromCharCode`不能识别大于`0xFFFF`的码点，所以`0x20BB7`就发生了溢出，最高位`2`被舍弃了，最后返回码点`U+0BB7`对应的字符，而不是码点`U+20BB7`对应的字符。 

ES6 提供了`String.fromCodePoint`方法，可以识别大于`0xFFFF`的字符，弥补了`String.fromCharCode`方法的不足。在作用上，正好与`codePointAt`方法相反。 

```
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true
```

上面代码中，如果`String.fromCodePoint`方法有多个参数，则它们会被合并成一个字符串返回。

注意，`fromCodePoint`方法定义在`String`对象上，而`codePointAt`方法定义在字符串的实例对象上。

------

## 字符串的遍历器接口

ES6 为字符串添加了遍历器接口（详见《Iterator》一章），使得字符串可以被`for...of`循环遍历。

```
for (let codePoint of 'foo') {
  console.log(codePoint)
}
// "f"
// "o"
// "o"
```

除了遍历字符串，这个遍历器最大的优点是可以识别大于`0xFFFF`的码点，传统的`for`循环无法识别这样的码点。 

```
let text = String.fromCodePoint(0x20BB7);

for(let i = 0; i < text.length; i++) {
    console.log(text[i]);
}

// ""
// ""
for(let i of text) {
    console.log(i);
}
//  "𠮷"
```

上面代码中，字符串`text`只有一个字符，但是`for`循环会认为它包含两个字符（都不可打印），而`for...of`循环会正确识别出这一个字符。 

------

## at()

ES5 对字符串对象提供`charAt`方法，返回字符串给定位置的字符。该方法不能识别码点大于`0xFFFF`的字符。

```
'abc'.charAt(0) // "a"
'𠮷'.charAt(0) // "\uD842"
```

上面代码中的第二条语句，`charAt`方法期望返回的是用2个字节表示的字符，但汉字“𠮷”占用了4个字节，`charAt(0)`表示获取这4个字节中的前2个字节，很显然，这是无法正常显示的。

目前，有一个提案，提出字符串实例的`at`方法，可以识别 Unicode 编号大于`0xFFFF`的字符，返回正确的字符。

```
'abc'.at(0) // "a"
'𠮷'.at(0) // "𠮷"
```

这个方法可以通过[垫片库](https://github.com/es-shims/String.prototype.at)实现。

------

## normalize()

许多欧洲语言有语调符号和重音符号。为了表示它们，Unicode 提供了两种方法。一种是直接提供带重音符号的字符，比如`Ǒ`（\u01D1）。另一种是提供合成符号（combining character），即原字符与重音符号的合成，两个字符合成一个字符，比如`O`（\u004F）和`ˇ`（\u030C）合成`Ǒ`（\u004F\u030C）。

这两种表示方法，在视觉和语义上都等价，但是 JavaScript 不能识别。

```
'\u01D1'==='\u004F\u030C' //false

'\u01D1'.length // 1
'\u004F\u030C'.length // 2
```

上面代码表示，JavaScript 将合成字符视为两个字符，导致两种表示方法不相等。

ES6 提供字符串实例的`normalize()`方法，用来将字符的不同表示方法统一为同样的形式，这称为 Unicode 正规化。 

```
'\u01D1'.normalize() === '\u004F\u030C'.normalize()
// true
```

`normalize`方法可以接受一个参数来指定`normalize`的方式，参数的四个可选值如下。

- `NFC`，默认参数，表示“标准等价合成”（Normalization Form Canonical Composition），返回多个简单字符的合成字符。所谓“标准等价”指的是视觉和语义上的等价。
- `NFD`，表示“标准等价分解”（Normalization Form Canonical Decomposition），即在标准等价的前提下，返回合成字符分解的多个简单字符。
- `NFKC`，表示“兼容等价合成”（Normalization Form Compatibility Composition），返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价，比如“囍”和“喜喜”。（这只是用来举例，`normalize`方法不能识别中文。）
- `NFKD`，表示“兼容等价分解”（Normalization Form Compatibility Decomposition），即在兼容等价的前提下，返回合成字符分解的多个简单字符。

```
'\u004F\u030C'.normalize('NFC').length // 1
'\u004F\u030C'.normalize('NFD').length // 2
```

上面代码表示，`NFC`参数返回字符的合成形式，`NFD`参数返回字符的分解形式。

不过，`normalize`方法目前不能识别三个或三个以上字符的合成。这种情况下，还是只能使用正则表达式，通过 Unicode 编号区间判断。
