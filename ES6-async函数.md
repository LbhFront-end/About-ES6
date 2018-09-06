# async 函数

1. [含义](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#含义)
2. [基本用法](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#基本用法)
3. [语法](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#语法)
4. [async 函数的实现原理](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#async-函数的实现原理)
5. [与其他异步处理方法的比较](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#与其他异步处理方法的比较)
6. [实例：按顺序完成异步操作](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#实例按顺序完成异步操作)
7. [异步遍历器](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-async函数.md#异步遍历器)

## 含义

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。

async 函数是什么，一句话，它就是 Generator 函数的语法糖。

前文有个 Generator 函数，依次读取两个文件。

```javascript
const fs = require('fs');

const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

写成 `async`函数，就是下面这样

```javascript
const asyncReadFile = async funtion() {
    const f1 = await readFile('/etc/fstab/');
    const f2 = await readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
}
```

一比较就会发现，`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已。

`async`函数对 Generator 函数的改进，体现在以下四点。

（1）内置执行器。

Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。

```javascript
asyncReadFile();
```

上面的代码调用了`asyncReadFile`函数，然后它就会自动执行，输出最后结果。这完全不像 Generator 函数，需要调用`next`方法，或者用`co`模块，才能真正执行，得到最后结果。

（2）更好的语义。

`async`和`await`，比起星号和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

（3）更广的适用性。

`co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象，而`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

（4）返回值是 Promise。

`async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。

进一步说，`async`函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖。

------
## 基本用法

`async`函数返回一个 Promise 对象，可以使用 `then`方法添加回调函数。当函数执行的时候，一旦遇到 `await`就会先返回等到异步操作完成，再接着执行函数体内后面的语句。

下面是一个例子

```javascript
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```

上面代码是一个获取股票报价的函数，函数前面的`async`关键字，表明该函数内部有异步操作。调用该函数时，会立即返回一个`Promise`对象。

下面是另一个例子，指定多少毫秒后输出一个值。



------
## 语法



------
## async 函数的实现原理



------
## 与其他异步处理方法的比较



------
## 实例：按顺序完成异步操作



------
## 异步遍历器



------

