# Symbol

1. [概述](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#概述)
2. [作为属性名的 Symbol](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#作为属性名的-symbol)
3. [实例：消除魔术字符串](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#实例消除魔术字符串)
4. [属性名的遍历](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#属性名的遍历)
5. [Symbol.for()，Symbol.keyFor()](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#symbolforsymbolkeyfor)
6. [Symbol.for()，Symbol.keyFor()](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#实例模块的-singleton-模式)
7. [内置的 Symbol 值](https://github.com/LbhFront-end/About-ES6/blob/master/Symbol.md#内置的-symbol-值)

## 概述

ES5的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入`Symbol`的原因。 

ES6引入了一种原始数据类型`Symbol`，表示独一无二的值。它是JavaScript语言的第七种数据类型，前六种是`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）

Symbol值通过`Symbol`函数生成，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种是新增的Symbol类型。凡是属性名属于Symbol类型，都是独一无二的，可以保证不会跟其他属性名发生冲突

```
let s = Symbol();

typeof s 
// "Symbol"
```

上面代码中，变量`s`就是一个独一无二的值。`typeof`运算符的结果，表明变量`s`是Symbol数据类型，而不是字符串之类的其他类型

注意，`Symbol`函数不能使用`new`命令，否则会报错。这是因为生成的Symbol是一个原始类型的值，不是对象。也就是说，由于Symbol值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型

`Symbol`函数可以接受一个字符串作为参数，表示对Symbol实例的描述，主要是为了再控制台显示，或者转为字符串时，比较容易区分

```
let s1 = Symbol('foo');
let s2 = Symbol('bar')

s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"
```

上面代码中，`s1`和`s2`是两个 Symbol 值。如果不加参数，它们在控制台的输出都是`Symbol()`，不利于区分。有了参数以后，就等于为它们加上了描述，输出的时候就能够分清，到底是哪一个值。

如果 Symbol 的参数是一个对象，就会调用该对象的`toString`方法，将其转为字符串，然后才生成一个 Symbol 值。

```
const obj = {
    toString(){
        return 'abc';
    }
}

const sym = Symbol(obj);
sym // Symbol(abc)
```

注意，`Symbol`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。 

```
// 没有参数的情况
let s1 = Symbol()
let s2 = Symbol()

s1 === s2 // false

// 有参数的情况下
let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2 // false
```

上面代码中，`s1`和`s2`都是`Symbol`函数的返回值，而且参数相同，但是它们是不相等的。

Symbol 值不能与其他类型的值进行运算，会报错。

```
let sym = Symbol('My symbol');

"your symbol is " + sym
// TypeError: can't convert symbol to string
`your symbol is ${sym}`
// TypeError: can't convert symbol to string
```

但是，Symbol 值可以显式转为字符串。

```
let sym = Symbol('My symbol');

String(sym) // 'Symbol(My symbol)'
sym.toString() // 'Symbol(My symbol)'
```

另外，Symbol 值也可以转为布尔值，但是不能转为数值。

```
let sym = Symbol();
Boolean(sym) // true
!sym  // false

if (sym) {
  // ...
}

Number(sym) // TypeError
sym + 2 // TypeError
```

------

## 作为属性名的 Symbol

------

## 实例：消除魔术字符串

------

## 属性名的遍历

------

## Symbol.for()，Symbol.keyFor()

------

## 实例：模块的 Singleton 模式

------

## 内置的 Symbol 值

------