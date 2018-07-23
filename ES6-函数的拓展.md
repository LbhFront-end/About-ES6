# 函数的扩展

1. [函数参数的默认值](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#函数参数的默认值)
2. [rest 参数](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#rest-参数)
3. [严格模式](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#严格模式)
4. [name 属性](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#name-属性)
5. [箭头函数](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#箭头函数)
6. [双冒号运算符](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#双冒号运算符)
7. [尾调用优化](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#尾调用优化)
8. [函数参数的尾逗号](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-函数的扩展.md#函数参数的尾逗号)





## 函数参数的默认值

### 基本用法

ES6 之前，不能直接为函数的参数指定默认值，只能采用变通的方法。

```
function log(x, y){
    y = y || 'World';
    console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello World
```

上面代码检查函数`log`的参数`y`有没有赋值。如果没有，则指定默认值为`World`。这种写法的缺点在于，如果参数`y`赋值了，但是对应的布尔值为`false`，则该赋值不起作用，就像上面代码的最后一行，参数`y`等于空字符，结果被改为默认值

为了避免这个问题，通常要先判断一下参数`y`是否被赋值，如果没有，再等于默认值

```
if(typeof y === 'undefined') {
    y = 'World'
}
```

ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面

```
function log(x, y = 'World') {
    console.log(x, y);
}
log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

可以看到，ES6 的写法比 ES5 简洁许多，而且非常自然。下面是另一个例子。 

```
function Point(x = 0, y = 0) {
    this.x = x;
    this.y = y
}
const p = new Point();
p // { x: 0, y: 0 }
```

除了简洁，ES6的写法还有两个好处，首先，阅读代码的人，可以意识到哪些参数是可以省略的，不用查看函数体或文档，其次，有利于将来代码的优化，即使未来的版本在对外接口中，彻底拿掉这个参数，也不会导致一起的代码无法运行

参数变量是默认声明的，所以不能用`let`或`const`再次声明

```
function foo (x = 5) {
    let x = 1; // error
    const x = 2; //error
}
```

上面代码中，参数变量`x`是默认声明的，在函数体内，不能用`let`或`const`再次声明，否则就会报错

使用参数默认值时，函数不能有同名参数

```
// 不报错
function foo(x, y, z) {
    //...
}
function foo(x, x, y = 1) {
    //...
}
// SyntaxError: Duplicate parameter name not allowed in this context
```

另外，一个容易忽略的地方是，参数默认值不是传值的，而是每次重新计算默认值表达式的值，也就是说，参数默认是惰性求值

```
let x = 99;
function foo(p = x + 1) {
    console.log(p)
}
foo() // 1000

x = 100;
foo() // 101
```

上面代码中，参数`p`的默认值是`x+1`，这时，每次调用函数foo,都会重新计算`x+1`，而不是默认`p`等于100

------

## rest 参数

------

## 严格模式

------

## name 属性

------

## 箭头函数

------

## 双冒号运算符

------

## 尾调用优化

------

## 函数参数的尾逗号

------