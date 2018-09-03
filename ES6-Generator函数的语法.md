# Generator 函数的语法

1. [简介](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#简介)
2. [next 方法的参数](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#next-方法的参数)
3. [for...of 循环](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#forof-循环)
4. [Generator.prototype.throw()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#generatorprototypethrow)
5. [Generator.prototype.return()](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#generatorprototypereturn)
6. [next()、throw()、return() 的共同点](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#nextthrowreturn-的共同点)
7. [yield\* 表达式](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#yield-表达式)
8. [作为对象属性的 Generator 函数](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#作为对象属性的-generator-函数)
9. [Generator 函数的this](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#generator-函数的this)
10. [含义](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#含义)
11. [应用](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-generator函数的语法.md#应用)



## 简介

### 基本概念

Generator 函数是ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

执行Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成的函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

形式上，Generator 函数是一个普通函数，但是有两个特征，一是，`function`关键字与函数名之间有一个星号；二是，函数内部使用 `yield`表达式，定义不同的内部状态（`yield`在英语里的意思是“产出”）

```javascript
function* helloWorldGenerator(){
    yield 'hello';
    yield 'world';
    return 'ending';
}
var hw = helloWorldGenerator();
```

上面代码定义了一个Generator 函数 `helloWorldFGenerator`,它内部有两个 `yield`表达式（`hello`和 `world`），即该函数有三个状态：hello，world，和 return  语句（结束执行）。

然后，Generator 函数的调用方法与普通函数一样，也是在函数后面加上一对括号。不同的是，调用Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。

下一步，必须调用遍历器对象的 `next`方法，使得指针移向下一个状态。也就是说，每次调用 `next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个 `yield`表达式（或 `return`）为止。换言之，Generator 函数是分段执行的，`yield`表达式时暂停执行的标记，而 `next`方法可以恢复执行。 

```javascript
hw.next();
// { value: 'hello', done: false }
hw.next()
// { value: 'world', done: false}
hw.next()
// {value: 'ending', done: true }
hw.next()
// {value: 'undefined', done: true }
```

上面代码一共调用了四次 `next`方法。

第一次调用，Generator 函数开始执行，直到遇到第一个 `yield` 表达式为止。 `next`方法返回一个对象，它的 `value`属性就是当前 `yield`表达式的值 是 `hello`，`done`属性的值 `false`表示遍历还没有结束。

第二次调用，Generator 函数从上次 `yield`表达式停下的地方，一直执行到下一个 `yield`表达式。 `next`方法返回的对象的 `value`属性就是当前 `yield`表达式的值 `world`, `done`属性的值 `false`，表示遍历还没有结束。

第三次调用，Generator 函数从上次`yield`表达式停下的地方，一直执行到`return`语句（如果没有`return`语句，就执行到函数结束）。`next`方法返回的对象的`value`属性，就是紧跟在`return`语句后面的表达式的值（如果没有`return`语句，则`value`属性的值为`undefined`），`done`属性的值`true`，表示遍历已经结束。

第四次调用，此时 Generator 函数已经运行完毕，`next`方法返回对象的`value`属性为`undefined`，`done`属性为`true`。以后再调用`next`方法，返回的都是这个值。

总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的`next`方法，就会返回一个有着`value`和`done`两个属性的对象。`value`属性表示当前的内部状态的值，是`yield`表达式后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束。

ES6 没有规定，`function`关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。

```javascript
function * foo(x, y) { ··· }
function *foo(x, y) { ··· }
function* foo(x, y) { ··· }
function*foo(x, y) { ··· }
```

由于 Generator 函数仍然是普通函数，所以一般的写法是上面的第三种，即星号紧跟在`function`关键字后面。本书也采用这种写法。

### yield 表达式 

由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

遍历器对象的`next`方法的运行逻辑如下。

1. 遇到`yield`表达式，就暂停执行后面的操作，并紧跟在 `yield` 后面的那个表达式的值，作为返回的对象的 `value` 属性值。
2. 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
3. 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
4. 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

需要注意的是，`yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

```javascript
function* gen() {
  yield  123 + 456;
}
```

上面代码中，`yield`后面的表达式`123 + 456`，不会立即求值，只会在`next`方法将指针移到这一句时，才会求值。

`yield`表达式与`return`语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到`yield`，函数暂停执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能。一个函数里面，只能执行一次（或者说一个）`return`语句，但是可以执行多次（或者说多个）`yield`表达式。正常函数只能返回一个值，因为只能执行一次`return`；Generator 函数可以返回一系列的值，因为可以有任意多个`yield`。从另一个角度看，也可以说 Generator 生成了一系列的值，这也就是它的名称的来历（英语中，generator 这个词是“生成器”的意思）。

Generator 函数可以不用`yield`表达式，这时就变成了一个单纯的暂缓执行函数。

```javascript
function *f(){
    console.log('执行了！');
}

var generator = f();
setTimeout(function(){
    generator.next();
}, 2000);
```

上面代码中，函数`f`如果是普通函数，在为变量`generator`赋值时就会执行。但是，函数`f`是一个 Generator 函数，就变成只有调用`next`方法时，函数`f`才会执行。

另外需要注意，`yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错。

```javascript
(function (){
  yield 1;
})()
// SyntaxError: Unexpected number
```

上面代码在一个普通函数中使用`yield`表达式，结果产生一个句法错误。

下面是另外一个例子。

```javascript
ar arr = [1, [[2, 3], 4], [5, 6]];

var flat = function* (a) {
  a.forEach(function (item) {
    if (typeof item !== 'number') {
      yield* flat(item);
    } else {
      yield item;
    }
  });
};

for (var f of flat(arr)){
  console.log(f);
}
```

上面代码也会产生句法错误，因为`forEach`方法的参数是一个普通函数，但是在里面使用了`yield`表达式（这个函数里面还使用了`yield*`表达式，详细介绍见后文）。一种修改方法是改用`for`循环。

```javascript
var arr = [1,[[2,3],4],[5,6]];
var flat = function* (a) {
  var length = a.length;
  for (var i = 0; i < length; i++) {
    var item = a[i];
    if (typeof item !== 'number') {
      yield* flat(item);
    } else {
      yield item;
    }
  }
};

for (var f of flat(arr)) {
  console.log(f);
}
// 1, 2, 3, 4, 5, 6
```

另外，`yield`表达式如果用在另一个表达式之中，必须放在圆括号里面。

```javascript
function* demo() {
  console.log('Hello' + yield); // SyntaxError
  console.log('Hello' + yield 123); // SyntaxError

  console.log('Hello' + (yield)); // OK
  console.log('Hello' + (yield 123)); // OK
}
```

`yield`表达式用作函数参数或放在赋值表达式的右边，可以不加括号。

```javascript
function* demo() {
  foo(yield 'a', yield 'b'); // OK
  let input = yield; // OK
}
```

### 与 Iterator 接口的关系

上一章说过，任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。

由于 Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。

```javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
}
[...myIterable] // [1, 2, 3]
```

上面代码中，Generator 函数赋值给`Symbol.iterator`属性，从而使得`myIterable`对象具有了 Iterator 接口，可以被`...`运算符遍历了。

Generator 函数执行后，返回一个遍历器对象。该对象本身也具有`Symbol.iterator`属性，执行后返回自身。

```javascript
function* gen(){
  // some code
}

var g = gen();

g[Symbol.iterator]() === g
// true
```

上面代码中，`gen`是一个 Generator 函数，调用它会生成一个遍历器对象`g`。它的`Symbol.iterator`属性，也是一个遍历器对象生成函数，执行后返回它自己。

------

## next 方法的参数



------

## for...of 循环

------

## Generator.prototype.throw()

------

## Generator.prototype.return()

------

## next()、throw()、return() 的共同点

------

## yield* 表达式

------

## 作为对象属性的 Generator 函数

------

## Generator 函数的this

------

## 含义

------

## 应用

------