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

`yield`表达式本身就没有返回值，或者说总是返回 `undefined`。`next`方法可以带一个参数，该参数就会被当作上一个 `yield`表达式的返回值。

```javascript
function* f(){
    for(var i=0; true; i++){
        var reset = yield i;
        if(reset){
            i = -1;
        }
    }
}
var g = f();
g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```

上面代码先定义了一个可以无限运行的 Generator 函数`f`，如果`next`方法没有参数，每次运行到`yield`表达式，变量`reset`的值总是`undefined`。当`next`方法带一个参数`true`时，变量`reset`就被重置为这个参数（即`true`），因此`i`会等于`-1`，下一轮循环就会从`-1`开始递增。

这个功能有很重要的语法意义。Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。也就是说，可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。

再看一个例子。

```javascript
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}
var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

上面代码中，第二次运行`next`方法的时候不带参数，导致 y 的值等于`2 * undefined`（即`NaN`），除以 3 以后还是`NaN`，因此返回对象的`value`属性也等于`NaN`。第三次运行`Next`方法的时候不带参数，所以`z`等于`undefined`，返回对象的`value`属性等于`5 + NaN + undefined`，即`NaN`。

如果向`next`方法提供参数，返回结果就完全不一样了。上面代码第一次调用`b`的`next`方法时，返回`x+1`的值`6`；第二次调用`next`方法，将上一次`yield`表达式的值设为`12`，因此`y`等于`24`，返回`y / 3`的值`8`；第三次调用`next`方法，将上一次`yield`表达式的值设为`13`，因此`z`等于`13`，这时`x`等于`5`，`y`等于`24`，所以`return`语句的值等于`42`。

注意，由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。V8 引擎直接忽略第一次使用`next`方法时的参数，只有从第二次使用`next`方法开始，参数才是有效的。从语义上讲，第一个`next`方法用来启动遍历器对象，所以不用带有参数。

再看一个通过`next`方法的参数，向 Generator 函数内部输入值的例子。

```javascript
function* dataConsumer() {
    console.log('Started');
    console.log(`1.${yield}`);
    console.log(`2.${yield}`);
    return 'result';
}
let genObj = dataConsumer();
genObj.next();
// Started
genObj.next('a')
// 1. a
genObj.next('b')
// 2. b
```

上面代码是一个很直观的例子，每次通过`next`方法向 Generator 函数输入值，然后打印出来。

如果想要第一次调用`next`方法时，就能够输入值，可以在 Generator 函数外面再包一层。

```javascript
function wrapper(generatorFunction) {
  return function (...args) {
    let generatorObject = generatorFunction(...args);
    generatorObject.next();
    return generatorObject;
  };
}

const wrapped = wrapper(function* () {
  console.log(`First input: ${yield}`);
  return 'DONE';
});

wrapped().next('hello!')
```

上面代码中，Generator 函数如果不用`wrapper`先包一层，是无法第一次调用`next`方法，就输入参数的。

------

## for...of 循环

`for...of`循环可以自动遍历 Generator 函数时生成的`Iterator`对象，且此时不再需要调用`next`方法。

```javascript
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```

上面代码使用`for...of`循环，依次显示 5 个`yield`表达式的值。这里需要注意，一旦`next`方法的返回对象的`done`属性为`true`，`for...of`循环就会中止，且不包含该返回对象，所以上面代码的`return`语句返回的`6`，不包括在`for...of`循环之中。

下面是一个利用 Generator 函数和`for...of`循环，实现斐波那契数列的例子。

```javascript
function* fibonacci(){
    let [prev, curr] = [0, 1];
    for(;;){
        yield curr;
        [prev, curr] = [curr, prev + curr];
    }
}
for(let n of fibonacci()){
    if(n > 1000) break;
    console.log(n);
}
```

从上面代码可见，使用`for...of`语句时不需要使用`next`方法。

利用`for...of`循环，可以写出遍历任意对象（object）的方法。原生的 JavaScript 对象没有遍历接口，无法使用`for...of`循环，通过 Generator 函数为它加上这个接口，就可以用了。

```javascript
function* objectEntries(obj) {
    let propKeys = Reflect.ownKeys(obj);
     for (let propKey of propKeys) {
        yield [propKey, obj[propKey]];
    }
}

let jane = { first: 'Jane', last: 'Doe' };
for (let [key, value] of objectEntries(jane)) {
  console.log(`${key}: ${value}`);
}
```

上面代码中，对象`jane`原生不具备 Iterator 接口，无法用`for...of`遍历。这时，我们通过 Generator 函数`objectEntries`为它加上遍历器接口，就可以用`for...of`遍历了。加上遍历器接口的另一种写法是，将 Generator 函数加到对象的`Symbol.iterator`属性上面。

```javascript
function* objectEntries() {
  let propKeys = Object.keys(this);

  for (let propKey of propKeys) {
    yield [propKey, this[propKey]];
  }
}

let jane = { first: 'Jane', last: 'Doe' };

jane[Symbol.iterator] = objectEntries;

for (let [key, value] of jane) {
  console.log(`${key}: ${value}`);
}
// first: Jane
// last: Doe
```

除了`for...of`循环以外，扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的，都是遍历器接口。这意味着，它们都可以将 Generator 函数返回的 Iterator 对象，作为参数。

```javascript
function* numbers () {
  yield 1
  yield 2
  return 3
  yield 4
}

// 扩展运算符
[...numbers()] // [1, 2]

// Array.from 方法
Array.from(numbers()) // [1, 2]

// 解构赋值
let [x, y] = numbers();
x // 1
y // 2

// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2
```

------

## Generator.prototype.throw()

Generator 函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获。

```javascript
var g = function* (){
    try{
        yield;
    }catch(e){
        console.log('内部捕获',e);
    }
}

var i = g();
i.next();

try{
    i.throw('a');
    i.throw('b');    
}catch(e){
    console.log('外部捕获', e);
}

// 内部捕获 a
// 外部捕获 b
```

上面代码中，遍历器对象`i`连续抛出两个错误。第一个错误被 Generator 函数体内的`catch`语句捕获。`i`第二次抛出错误，由于 Generator 函数内部的`catch`语句已经执行过了，不会再捕捉到这个错误了，所以这个错误就被抛出了 Generator 函数体，被函数体外的`catch`语句捕获。

`throw`方法可以接受一个参数，该参数会被`catch`语句接收，建议抛出`Error`对象的实例。

```javascript
var g = function* (){
    try{
        yield;
    }catch(e){
        console.log(e);
    }
};

var i = g();
i.next();
i.throw(new Error('出错了！ '));

// Error: 出错了！(…)
```

注意，不要混淆遍历器对象的`throw`方法和全局的`throw`命令。上面代码的错误，是用遍历器对象的`throw`方法抛出的，而不是用`throw`命令抛出的。后者只能被函数体外的`catch`语句捕获。

```javascript
var g = function* () {
  while (true) {
    try {
      yield;
    } catch (e) {
      if (e != 'a') throw e;
      console.log('内部捕获', e);
    }
  }
};

var i = g();
i.next();

try {
  throw new Error('a');
  throw new Error('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 外部捕获 [Error: a]
```

上面代码之所以只捕获了`a`，是因为函数体外的`catch`语句块，捕获了抛出的`a`错误以后，就不会再继续`try`代码块里面剩余的语句了。

如果 Generator 函数内部没有部署`try...catch`代码块，那么`throw`方法抛出的错误，将被外部`try...catch`代码块捕获。

```javascript
var g = function* () {
  while (true) {
    yield;
    console.log('内部捕获', e);
  }
};

var i = g();
i.next();

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 外部捕获 a
```

上面代码中，Generator 函数`g`内部没有部署`try...catch`代码块，所以抛出的错误直接被外部`catch`代码块捕获。

如果 Generator 函数内部和外部，都没有部署`try...catch`代码块，那么程序将报错，直接中断执行。

```javascript
var gen = function* gen(){
  yield console.log('hello');
  yield console.log('world');
}

var g = gen();
g.next();
g.throw();
// hello
// Uncaught undefined
```

上面代码中，`g.throw`抛出错误以后，没有任何`try...catch`代码块可以捕获这个错误，导致程序报错，中断执行。

`throw`方法抛出的错误要被内部捕获，前提是必须至少执行过一次`next`方法。

```javascript
function* gen() {
  try {
    yield 1;
  } catch (e) {
    console.log('内部捕获');
  }
}

var g = gen();
g.throw(1);
// Uncaught 1
```

上面代码中，`g.throw(1)`执行时，`next`方法一次都没有执行过。这时，抛出的错误不会被内部捕获，而是直接在外部抛出，导致程序出错。这种行为其实很好理解，因为第一次执行`next`方法，等同于启动执行 Generator 函数的内部代码，否则 Generator 函数还没有开始执行，这时`throw`方法抛错只可能抛出在函数外部。

`throw`方法被捕获以后，会附带执行下一条`yield`表达式。也就是说，会附带执行一次`next`方法。

```javascript
var gen = function* gen(){
  try {
    yield console.log('a');
  } catch (e) {
    // ...
  }
  yield console.log('b');
  yield console.log('c');
}

var g = gen();
g.next() // a
g.throw() // b
g.next() // c
```

上面代码中，`g.throw`方法被捕获以后，自动执行了一次`next`方法，所以会打印`b`。另外，也可以看到，只要 Generator 函数内部部署了`try...catch`代码块，那么遍历器的`throw`方法抛出的错误，不影响下一次遍历。

另外，`throw`命令与`g.throw`方法是无关的，两者互不影响。

```javascript
var gen = function* gen(){
  yield console.log('hello');
  yield console.log('world');
}

var g = gen();
g.next();

try {
  throw new Error();
} catch (e) {
  g.next();
}
// hello
// world
```

上面代码中，`throw`命令抛出的错误不会影响到遍历器的状态，所以两次执行`next`方法，都进行了正确的操作。

这种函数体内捕获错误的机制，大大方便了对错误的处理。多个`yield`表达式，可以只用一个`try...catch`代码块来捕获错误。如果使用回调函数的写法，想要捕获多个错误，就不得不为每个函数内部写一个错误处理语句，现在只在 Generator 函数内部写一次`catch`语句就可以了。

Generator 函数体外抛出的错误，可以在函数体内捕获；反过来，Generator 函数体内抛出的错误，也可以被函数体外的`catch`捕获。

```javascript
function* foo() {
  var x = yield 3;
  var y = x.toUpperCase();
  yield y;
}

var it = foo();

it.next(); // { value:3, done:false }

try {
  it.next(42);
} catch (err) {
  console.log(err);
}
```

上面代码中，第二个`next`方法向函数体内传入一个参数 42，数值是没有`toUpperCase`方法的，所以会抛出一个 TypeError 错误，被函数体外的`catch`捕获。

一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了。如果此后还调用`next`方法，将返回一个`value`属性等于`undefined`、`done`属性等于`true`的对象，即 JavaScript 引擎认为这个 Generator 已经运行结束了。

```javascript
function* g() {
  yield 1;
  console.log('throwing an exception');
  throw new Error('generator broke!');
  yield 2;
  yield 3;
}

function log(generator) {
  var v;
  console.log('starting generator');
  try {
    v = generator.next();
    console.log('第一次运行next方法', v);
  } catch (err) {
    console.log('捕捉错误', v);
  }
  try {
    v = generator.next();
    console.log('第二次运行next方法', v);
  } catch (err) {
    console.log('捕捉错误', v);
  }
  try {
    v = generator.next();
    console.log('第三次运行next方法', v);
  } catch (err) {
    console.log('捕捉错误', v);
  }
  console.log('caller done');
}

log(g());
// starting generator
// 第一次运行next方法 { value: 1, done: false }
// throwing an exception
// 捕捉错误 { value: 1, done: false }
// 第三次运行next方法 { value: undefined, done: true }
// caller done
```

面代码一共三次运行`next`方法，第二次运行的时候会抛出错误，然后第三次运行的时候，Generator 函数就已经结束了，不再执行下去了。

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