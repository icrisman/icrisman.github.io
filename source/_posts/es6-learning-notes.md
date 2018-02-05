---
title: ES6 学习笔记 (ing)
date: 2016-10-14 19:07:06
categories: front-end
tags: ES6
toc: true
---

> 我只是一个简单的学习笔记。

learn from this book:  [《ECMAScript 6 入门》](http://es6.ruanyifeng.com/) by 阮一峰

<!-- more -->
 
<!-- toc -->

## 0. 发展历史

- 1996.11，ECMAScript 1.0发布
- 1998.6，ECMAScript 2.0发布
- 1999.12，ECMAScript 3.0发布
- 2000~2007.10，ECMAScript 4.0草案发布，最终因分歧较大未发布正式版本
- 2009.12，ECMAScript 5.0
- 2011.6，ECMAScript 5.1发布
- 2015.6，ES2015（《ECMAScript 2015标准》）发布
- 2016.6，ES2016（《ECMAScript 2016标准》）发布

> ES6既是一个历史名词，也是一个泛指，含义是5.1版以后的JavaScript的下一代标准，涵盖了ES2015、ES2016、ES2017等等，而ES2015则是正式名称，特指该年发布的正式版本的语言标准。

> 本书中提到“ES6”的地方，一般是指ES2015标准，但有时也是泛指“下一代JavaScript语言”。

ECMAScript 与 Javascript 的区别是：前者是后者的规格，后者是前者的一种实现。

## 1. 环境

### node (推荐使用nvm管理node版本)
```
    ## 安装nvm，<version number>需要用版本号替换，目前是v0.32.0
    $ curl -o- https://raw.githubusercontent.com/creationix/nvm/<version number>/install.sh | bash

    ## 激活nvm
    $ source ~/.nvm/nvm.sh

    ## 列出远程node版本
    $ nvm ls-remote

    ## 安装指定版本的node
    $ nvm install v4.1.2

    ## 切换到指定版本
    $ nvm use v4.1.2

    ## 查看当前已安装的node版本
    $ nvm ls

    ## 设置指定版本为默认的node版本
    $ nvm alias default stable

    ## 查看当前node版本
    $ node -v

    ## 查看node安装目录
    $ which node

    ## npm安装的全局目录
    $ npm config get prefix
    $ npm config set prefix /usr/local/
```

### Babel转码工具

由于浏览器暂不支持 es6 的解析，所以需要使用Babel，第一步是添加配置
有以下两种方式:

1. 在项目根目录添加配置文件 `.babelrc`。
```
    {
        // 设定转码规则
        "presets": ["es2015"]
    }
```
2. 在 `package.json` 添加如下配置
```
    "babel": {
     　　"presets": ["es2015", "stage-2"]
    }
```

[你可以在这里测试转码效果](http://babeljs.io/repl/)

> [Babel官网](http://babeljs.io/docs/setup/#installation) 推荐将 babel-cli 安装在项目本地，原因有二：
- Different projects on the same machine can depend on different versions of Babel allowing you to update one at a time.
- It means you do not have an implicit dependency on the environment you are working in. Making your project far more portable and easier to setup.

- 一台机器上的不同项目可能会基于不同版本的 Babel ，这样允许你一次升级一个项目，不会互相影响
- 这意味着在你工作的环境下没有含义不明的依赖。使你的项目更轻便、更容易安装。

[[这里还有其它的使用方式]](http://babeljs.io/docs/setup/#installation)

## 2. 主要内容

### 数据类型变化

JS 中的数据类型有：undefined null String Number Boolean Object

#### 新增 symbol 类型

```
// 创建一个新的symbol，它的值与其它任何值皆不相等：
var mySymbol = Symbol()

// 创建一个独一无二的symbol：
var isMoving = Symbol("isMoving")
```

- 三种获取 Symbol 的方式
    1. Symbol()
    2. Symbol.for(string)
    3. 使用标准定义的 Symbol，如 `Symbol.iterator`

- tips
    - symbol 不能被自动转为字符串或数字，但可以转为 boolean；
    - symbol 作为属性时不能被 for...in、 `Object.keys()`等遍历到，可以使用`Object.getOwnPropertySymbols(obj)` 或`Reflect.ownKeys(obj)`，前者返回对象的 symbol 键，后者返回所有字符串键和 symbol 键；
    - Symbol.keyFor(s1) 返回 symbol s1的 key 值

#### Object

- Object.defineProperty()
- Object.freeze()
- Object.seal()

### 语法变化

#### 变量声明

- let：声明变量，类似var。与var区别如下：
    - **块级作用域**。只在let所在代码块内有效，for语句的计数器很适合用let声明；
    - **先声明后使用**。不存在变量提升；暂时性死区，在一个作用域内所有变量都需要先声明后使用，否则会导致ReferenceError；
    - **不允许重复声明**；

- const：声明常量。
    - 除 **只读** 外，其它特性与let类似。
    - const定义引用类型时，表示的是地址可读，如对象或数组，其值仍然可修改，如果要冻结，可使用`Object.freeze()`;

    ```
    const foo = Object.freeze({});

    // 常规模式时，下面一行不起作用；
    // 严格模式时，该行会报错
    foo.prop = 123;
    ```

- class

通常我们这样实现一个类：
```
function Circle(radius) {
    this.radius = radius;
    Circle.circlesMade++;
}
Circle.draw = function draw(circle, canvas) { /* Canvas绘制代码 */ }
Object.defineProperty(Circle, "circlesMade", {
    get: function() {
        return !this._count ? 0 : this._count;
    },
    set: function(val) {
        this._count = val;
    }
});
Circle.prototype = {
    area() {
        return Math.pow(this.radius, 2) * Math.PI;
    },
    get radius() {
        return this._radius;
    },
    set radius(radius) {
        if (!Number.isInteger(radius))
            throw new Error("圆的半径必须为整数。");
        this._radius = radius;
    }
};
```

现在，新增了`class` 关键字用来声明一个类，像这样：

```
class Circle {
    constructor(radius) {
        this.radius = radius;
        Circle.circlesMade++;
    };
    static draw(circle, canvas) {
        // Canvas绘制代码
    };
    static get circlesMade() {
        return !this._count ? 0 : this._count;
    };
    static set circlesMade(val) {
        this._count = val;
    };
    area() {
        return Math.pow(this.radius, 2) * Math.PI;
    };
    get radius() {
        return this._radius;
    };
    set radius(radius) {
        if (!Number.isInteger(radius))
            throw new Error("圆的半径必须为整数。");
        this._radius = radius;
    };
}
```

同时，还增加了`extends`来实现继承：

```
class Shape {
    get color() {
        return this._color;
    }
    set color(c) {
        this._color = parseColorAsRGB(c);
        this.markChanged();  // 稍后重绘Canvas
    }
}

class Rectangle extends Shape {

}
```

> ES5中，var或function定义的全局变量与顶层对象(window/global)的属性等价。
> ES6中，let、const、class定义的全局变量不再跟顶层对象有关联。

```
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```

#### 模板字符串

- 基础使用方法
```
var str = 'world'
var result = `hello ${str}` // 'hello world'
```

- 标签模板 tagged templates
语法：模板字符串开始的反撇号前附加一个额外的标签，可以是标识符、属性值或方法调用等任何 ES6的成员表达式或调用表达式。

```
var message = SaferHTML`<p>${bonk.sender} 向你示好。</p>`;

// 等效于
var message = SaferHTML(templateData, bonk.sender);

// SaferHTML是一个普通的函数，它可以是这样：
function SaferHTML(templateData) {
  var s = templateData[0];
  for (var i = 1; i < arguments.length; i++) {
    var arg = String(arguments[i]);

    // 转义占位符中的特殊字符。
    s += arg.replace(/&/g, "&")
            .replace(/</g, "<")
            .replace(/</g, ">");

    // 不转义模板中的特殊字符。
    s += templateData[i];
  }
  return s;
}
```

#### 不定参数、默认参数

- 不定参数

    1. 使用 `arguments`解决
    2. 使用 `...` 展开运算符

    ```
    // needles 是 haystack 后面所有参数组成的数组
    function containsAll(haystack, ...needles) {
        for (var needle of needles) {
            if (haystack.indexOf(needle) === -1) {
                return false
            }
        }
        return true
    }
    ```

- 默认参数
形参部分通过赋值语句可以设置默认参数，从左至右依次赋值

```
function getItemById (id = 0) {
    return {
        id,
        name: 'moly'
    }
}
```


#### 解构

- 对象赋值模式
    
    被解构的值需要被强制转换为对象。因此 null 和 undefined 不能被解构。
    ```
    var {a, b} = {a: 1, b: 2} // a: 1, b: 2
    var {a: name} = {a: 'moly'} // name: moly
    ```

- 数组赋值模式

    被解构的值一定要包含一个迭代器。同样 null 和 undefined 不可迭代。
    ```
    var [a, b] = [1, 2, 3]
    var [head, ...tail] = [1, 2, 3, 4]; // head: 1,  tail: [2, 3, 4]
    ```

- 用途总结
    - 交换变量的值

        ```
        [x, y] = [y, x]
        ```
    - 函数参数定义

        ```
        // 参数是一组有次序的值
        function f([x, y, z]) { ... }
        f([1, 2, 3])

        // 参数是一组无次序的值
        function f({x, y, z}) { ... }
        f({z: 3, y: 2, x: 1})
        ```

    - 函数参数的默认值

        ```
        jQuery.ajax = function (url, {
            async = true,
            beforeSend = function () {},
            cache = true,
            complete = function () {},
            crossDomain = false,
            global = true,
            // ... more config
        }) {
          // ... do stuff
        };
        // 避免了 var foo = config.foo || 'default foo' 这样的语句
        ```
        
    - 遍历Map

        ```
        var map = new Map()
        map.set('first', 'hello')
        map.set('second', 'world')
        // [key] 或 [, value] 可以只获取key或只获取value
        for (let [key, value] of map) {
            console.log(key + " is " + value)
        }
        // first is hello
        // second is world
        ```

    - 多重返回值
    ```
    // 数组形式
    function returnMultipleValues() {
        return [1, 2]
    }
    var [foo, bar] = returnMultipleValues()

    // 对象形式
    function returnMultipleValues() {
        return {
            foo: 1,
            bar: 2
        }
    }
    var {foo, bar} = returnMultipleValues()
    ```

    - 输入模块的指定方法

        ```
        const {SourceMapConsumer, SourceNode} = require("source-map");
        // 类似 `import` 的语法
        ```

#### 箭头函数

```
var sum = (a, b) => a + b
sum(2, 3) // 5
```

- 箭头函数没有自己的 this 值，它的 this 继承自外围作用域
- 箭头函数没有自己的 arguments 值，可以使用不定参数和默认参数来实现

详细的语法参考 [这里](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

#### 循环

- 最初我们使用普通的 for 循环遍历数组
```
var arr = [1, 2, 3]
for (var i = 0; i < arr.length; i++) {
    console.log(i, arr[i])
}
```

- ES5之后，使用 Array.prototype.forEach
```
arr.forEach(function (val) {
    console.log(val)
})
```
forEach 的缺点： 不能使用 break 中断循环；不能使用 return 返回外层。

- 或许也可以试一下 for-in 循环
```
for (var index in arr) {
    console.log(arr[index])
}
```
for-in 的缺点：循环变量 index 不是数字而是字符串；遍历的不仅包含数组元素还可能包含自定义属性，如 arr.name；更甚，遍历结果可能按随机顺序 orz！

其实 for-in 是为遍历对象而设计的。

- 请使用 for-of 循环吧
```
for (var val of arr) {
    console.log(val)
}
```
简洁而正确，可正确响应 `break`、`continue`、`return`。
它为遍历各种集合而设计，实际是调用集合的`[Symbol.iterator]()`方法。
但它也有缺点，那就是无法获取索引值。
```
// 因为jQuery对象与数组相似，可以为其添加与数组一致的迭代器方法
jQuery.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator]
// 这样就可以使用 for-of 遍历 jQuery 对象了
for (var $item of $('div')) {
    console.log($item)
}
```

#### 迭代器

Array/Set/Map 都有一个迭代器方法，也就是`[Symbol.iterator]()`。可以为任何对象添加这个方法，拥有这个方法的对象被称为`可迭代的`。

迭代器是一个拥有 `next` 方法的对象，遍历这个对象时，每个循环都调用 next 方法。

```
var zeroesForeverIterator = {
    [Symbol.iterator] () {
        return this
    },
    next () {
        return {done: false, value: 0};
    }
}
```

#### 生成器

生成器是一种特殊的函数，它与函数有以下不同：
1. 使用 `function*` 声明；
2. 函数内部可以写无数个 `yield` 语句，类似`return`；
3. 调用生成器函数，返回一个暂停的生成器对象；
4. 每当调用生成器对象的 `.next()` 方法，函数将开启运行直到下一个`yield`语句，并返回对象 `{value: 'yield 后的值', done: false}`，执行到最后一个 yield 时，返回对象的 done 值为 true。

```
function* quips (name) {
    yield 'this'
    yield 'is'
    yield name + '\'s'
    yield 'generator'
}

var q = quips('celine')
q.next()  // {value: 'this', done: false}
q.next()  // {value: 'is', done: false}
q.next()  // {value: 'celine's', done: false}
q.next()  // {value: 'generator', done: true}
```

这个奇怪的东西是用来干嘛的呢？

假如我想实现一个 range 迭代器，像这样：
```
// 应该弹出三次 "ding"
for (var value of range(0, 3)) {
    alert("Ding! at floor #" + value);
}

// 方法一：使用 ES6的 class 实现
class RangeIterator {
  constructor(start, stop) {
    this.value = start;
    this.stop = stop;
  }

  [Symbol.iterator]() { return this; }

  next() {
    var value = this.value;
    if (value < this.stop) {
      this.value++;
      return {done: false, value: value};
    } else {
      return {done: true, value: undefined};
    }
  }
}

// 返回一个新的迭代器，可以从start到stop计数。
function range(start, stop) {
  return new RangeIterator(start, stop);
}

// 方法二：用生成器
function* range (start, stop) {
    for (var i = start; i < stop; i++) {
        yield i;
    }
}
```
就是这么简洁明了，我和我的小伙伴都惊呆了！

> 生成器是迭代器。

#### 模块

- es6中，一个js 文件是一个模块，默认为严格模式；
- 在模块中可以使用 import 来引入模块，用 export 来导出模块。

#### 集合

#### 代理 Proxy
