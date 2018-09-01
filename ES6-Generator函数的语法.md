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

```
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

```
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

第一次调用，Generator 函数开始执行，直到遇到第一个 `yield` 表达式为止。 `next`方法返回一个对象，它的 `value`属性就是当前 `yield`表达式的值 是 `hello`，`done`属性的值 `false`

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