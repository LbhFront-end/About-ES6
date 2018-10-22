# ECMAScript 6 入门



## 一、简介

#### 1. ECMAScript  和 JavaScript 的关系

前者是后者的规格，后者是前者的一种实现

------

#### 2. ES6 与 ECMAScript 2015 的关系

ES6 是一个历史名词，也是一个泛指，含义是 5.1 版本以后的 JavaScript 的一代标准，涵盖了 ES2015、ES2016、ES2017 等等，而 ES2015 则是正式名称，特指该年发布的正式版本的标准。

------

#### 3. 部署进度

浏览器的最新版本，随着时间的推移，对 ES6 的支持越来越高了。

Node 是 JavaScript 的服务器运行环境 （runtime）。它对 ES6 的支持度更高，除了那些默认打开的功能，还有一些语法功能已经实现了，但是默认没有打开。使用以下命令，可以看 Node 已经实现的 ES6 的特征

```bash
$ node --v8-options | grep harmony
```

上面的输出的结果，会因为版本的不同而不同

#### 4. Babel 转码器

Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在现有的环境执行

```javascript
// 转码前
input.map(item => item +1);

//转码后
input.map(function(item){
    return item + 1;
});
```

##### 配置文件.babelrc  

Babel 的配置文件是 `.babelrc` ，存放在项目的根目录下

```javascript
{
    "presets" : [],
    "plugins" : []
}
```

`persets` 字段设定转码规则，可以根据官方的规则按需下载

```bash
 # 最新转码规则
 $ npm i -S -D babel-preset-latest
 
 # react 转码规则
 $ npm i -S -D babel-preset-react
 
 # 不同阶段语法提案的转码规则（共有4个阶段），选装一个
 $ npm i -S -D babel-preset-stage-0
 $ npm i -S -D babel-preset-stage-1
 $ npm i -S -D babel-preset-stage-2
 $ npm i -S -D babel-preset-stage-3
```

然后，将规则加入到 `.babelrc` 

```javascript
{
    "presets" : [
        "lastest",
        "react",
        "stage-2"
    ],
    "plugins" : []
}
```

##### 命令行转码 `babel-cli`

Babel 提供 `babel-cli` 工具，用于命令行的转码

```bash
 $ npm i -g babel-cli
```

基本用法

```bash
# 转码结果输出到标准输出
$ babel example.js

# 转码结果写入一个文件
# --out-file 或 -o 参数指定输出文件
$ babel example.js --out-file compiled.js
# 或者
$ babel example.js -o compiled.js

# 整个目录转码
# --out-dir 或 -d 参数指定输出目录
$ babel src --out-dir lib
# 或者
$ babel src -d lib

# -s 参数生成source map文件
$ babel src -d lib -s
```

上面代码是在全局环境下，进行 Babel 转码。这意味着，如果项目要运行，全局环境必须有 Babel，也就是说项目产生了对环境的依赖。另一方面，这样做也无法支持不同项目使用不同版本的 Babel。 

一个解决办法是将`babel-cli`安装在项目之中。 

```bash
# 安装
$ npm install --save-dev babel-cli
```

然后，改写`package.json`。

```javascript
{
  // ...
  "devDependencies": {
    "babel-cli": "^6.0.0"
  },
  "scripts": {
    "build": "babel src -d lib"
  },
}
```

转码的时候，就执行下面的命令。

```bash
$ npm run build
```

##### babel-node

`babel-cli`工具自带一个`babel-node`命令，提供一个支持 ES6 的 REPL 环境。它支持 Node 的 REPL 环境的所有功能，而且可以直接运行 ES6 代码。

它不用单独安装，而是随`babel-cli`一起安装。然后，执行`babel-node`就进入 REPL 环境。

```bash
$ babel-node
> (x => x * 2)(1)
2
```

`babel-node`命令可以直接运行 ES6 脚本。将上面的代码放入脚本文件`es6.js`，然后直接运行。

```bash
$ babel-node es6.js
2
```

`babel-node`也可以安装在项目中。

```bash
$ npm install --save-dev babel-cli
```

然后，改写`package.json`。

```javascript
{
  "scripts": {
    "script-name": "babel-node script.js"
  }
}
```

上面代码中，使用`babel-node`替代`node`，这样`script.js`本身就不用做任何转码处理。

### babel-register

`babel-register`模块改写`require`命令，为它加上一个钩子。此后，每当使用`require`加载`.js`、`.jsx`、`.es`和`.es6`后缀名的文件，就会先用 Babel 进行转码。

```bash
$ npm install --save-dev babel-register
```

使用时，必须首先加载`babel-register`。

```javascript
require("babel-register");
require("./index.js");
```

然后，就不需要手动对`index.js`转码了。

需要注意的是，`babel-register`只会对`require`命令加载的文件转码，而不会对当前文件转码。另外，由于它是实时转码，所以只适合在开发环境使用。

### babel-core

如果某些代码需要调用 Babel 的 API 进行转码，就要使用`babel-core`模块。

安装命令如下。

```bash
$ npm install babel-core --save
```

然后，在项目中就可以调用`babel-core`。

```javascript
var babel = require('babel-core');

// 字符串转码
babel.transform('code();', options);
// => { code, map, ast }

// 文件转码（异步）
babel.transformFile('filename.js', options, function(err, result) {
  result; // => { code, map, ast }
});

// 文件转码（同步）
babel.transformFileSync('filename.js', options);
// => { code, map, ast }

// Babel AST转码
babel.transformFromAst(ast, code, options);
// => { code, map, ast }
```

配置对象`options`，可以参看官方文档<http://babeljs.io/docs/usage/options/>。

下面是一个例子。

```javascript
var es6Code = 'let x = n => n + 1';
var es5Code = require('babel-core')
  .transform(es6Code, {
    presets: ['latest']
  })
  .code;
// '"use strict";\n\nvar x = function x(n) {\n  return n + 1;\n};'
```

上面代码中，`transform`方法的第一个参数是一个字符串，表示需要被转换的 ES6 代码，第二个参数是转换的配置对象。

### babel-polyfill

Babel 默认只转换新的 JavaScript 句法（syntax），而不转换新的 API，比如`Iterator`、`Generator`、`Set`、`Maps`、`Proxy`、`Reflect`、`Symbol`、`Promise`等全局对象，以及一些定义在全局对象上的方法（比如`Object.assign`）都不会转码。

举例来说，ES6 在`Array`对象上新增了`Array.from`方法。Babel 就不会转码这个方法。如果想让这个方法运行，必须使用`babel-polyfill`，为当前环境提供一个垫片。

安装命令如下。

```bash
$ npm install --save babel-polyfill
```

然后，在脚本头部，加入如下一行代码。

```javascript
import 'babel-polyfill';
// 或者
require('babel-polyfill');
```

Babel 默认不转码的 API 非常多，详细清单可以查看`babel-plugin-transform-runtime`模块的[definitions.js](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-runtime/src/definitions.js)文件。

##### 浏览器环境

Babel 也可以用于浏览器环境。但是，从 Babel 6.0 开始，不再直接提供浏览器版本，而是要用构建工具构建出来。如果你没有或不想使用构建工具，可以使用[babel-standalone](https://github.com/Daniel15/babel-standalone)模块提供的浏览器版本，将其插入网页。

```javascript
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.4.4/babel.min.js"></script>
<script type="text/babel">
// Your ES6 code
</script>
```

注意，网页实时将 ES6 代码转为 ES5，对性能会有影响。生产环境需要加载已经转码完成的脚本。

下面是如何将代码打包成浏览器可以使用的脚本，以`Babel`配合`Browserify`为例。首先，安装`babelify`模块。

```bash
$ npm install --save-dev babelify babel-preset-latest
```

然后，再用命令行转换 ES6 脚本。

```bash
$  browserify script.js -o bundle.js \
  -t [ babelify --presets [ latest ] ]
```

上面代码将 ES6 脚本`script.js`，转为`bundle.js`，浏览器直接加载后者就可以了。

在`package.json`设置下面的代码，就不用每次命令行都输入参数了。

```javascript
{
  "browserify": {
    "transform": [["babelify", { "presets": ["latest"] }]]
  }
}
```