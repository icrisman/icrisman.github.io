---
title: JS 基础 (上)
date: 2017-09-06 11:00:00
categories: front-end
tags: javascript
toc: true
---

<div style="text-align: center">-·-·-·-·-·-·-·-·-· agenda ·-·-·-·-·-·-·-·-·-</div>

<!-- toc -->

<!-- more -->

<div style="text-align: center">-·-·-·-·-·-·-·-·-· Let's start ·-·-·-·-·-·-·-·-·-</div>

## 数据类型

5种基本数据类型：undefined、null、String、Boolean、Number

1种引用数据类型：Object

es6 新增：Symbol
 
### 数据类型知识点

-  判断类型

    判断基础类型：typeof

    判断引用类型：
    - instanceof
    - constructor：除了 undefined 和 null 均可以用此判断出类型，缺点是可以被修改
    - Object.prototype.toString.call(a)
    ```
    Object.prototype.toString.call({})  // "[object Object]"
    Object.prototype.toString.call([])  // "[object Array]"
    Object.prototype.toString.call(/^\d$/)  // "[object RegExp]"
    ```

- undefined

    对未声明的变量使用 `typeof` 返回 `undefined`。

    为区分“未声明”与“未初始化”推荐显式声明变量，没有必要将变量显式声明为 undefined。

- null

    null意为“空对象”，`typeof null` 返回 `object`。

    由于 `null==undefined` 为 `true`，为区分类型，因此当一个变量意在保存对象但还没保存时，可显式声明为 null。

- number

    包括数值、NaN、 Infinity。

    `NaN==NaN` 返回 false

    - Math 对象
        - `Math.ceil()`    向上舍入（变大）
        - `Math.round()`    四舍五入
        - `Math.floor()`    向下舍入（变小）

    - Number.prototype.toFixed()
        - `num.toFixed(2)`    在数值上调用，返回string（常用于价格等的舍入）
 
### 数据类型转换

一张图看懂格式转换：

![一张图看懂格式转换](/img/js_basic20170906/format.png)

易错点在这里：

- 转换为数值

    - Number()  /  +

        `undefined`:  NaN

        `null`:  0

        `boolean`:  0/1

        `string`:  ""->0, "1.2"->1.2,  "123string"->NaN

        `object`: 先调用valueOf()，没有的话调用toString()

    - parseInt('string',  进制)  按进制解析字符串

    - parseFloat('string')  始终按十进制解析字符串

- 转换为字符串

    `toString()`，除 undefined、null 外其它值都有此方法

    `num.toString(基数)` 表示转换为对应进制的值，默认为10
 
## 作用域、闭包、this

- 作用域

    js中没有块级作用域，而是函数级作用域。

- 作用域链

    每个函数都有自己的执行环境，当代码在一个环境中执行时，会创建变量对象的作用域链。作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。

    每个作用域是一个执行上下文环境，整体遵循`栈`的规则。

    从`全局执行环境`开始，按照包含关系压入栈内，每个作用域只能访问到当前和被包含的环境的变量，而不能访问到它包含的作用域内的变量。

    一个函数执行完毕后，它所生成的执行环境出栈，并且被自动销毁。当然这是最理想的情况，有时函数执行结束后，还不能将其执行环境销毁，就是下面要说的闭包了。

- 闭包

    闭包是指有权访问另一个函数作用域中的变量的函数。

    主要两种情况：函数作为返回值、函数作为参数传递

    - 函数作为返回值

        ```
        function fun () {
            var n = 100

            return function (x) {
                return x > n ? x : n
            }
        }

        var f = fun()
        f()  // 100
        f(200)  // 200
        ```

    - 函数作为参数传递

- this
    this的值取决于调用的模式。

    js中有4种调用模式：构造函数调用、方法调用、函数调用、apply调用

    > 方法：函数作为对象中的属性时叫做方法

    - 构造函数调用

        构造函数中的 this 指代的是：即将通过此函数 new 出来的对象

        ```
        function Fun () {
            this.name = 'fun'
            console.log(this)
        }

        new Fun()  // Fun {name: "fun"}
        Fun()  // Window {stop: function, open: function, alert: function, confirm: function, prompt: function…}
        ```

    - 方法调用

        方法作对象的一个属性被调用时，方法中的 this 指向该对象。

        ```
        var name = 'deshan'
        var obj = {
            name: 'zhenzhu',
            getName: function () {
                return this.name
            }
        }

        obj.getName() // 'zhenzhu'

        var getName = obj.getName
        getName()  // 'deshan'
        ```

    - 函数调用

        普通函数中的 this 指向 window 喽

    - apply 调用

        一个函数被 apply/call 调用时，函数中的 this 指向第一个参数对象。

        Nothing to say.

## 面向对象、原型链

### 原型 prototype

函数是对象，而对象是属性的集合，js 默认给每一个函数赋予原型属性 —— 即 prototype，它是个对象。prototype 对象默认有个 constructor 属性。

```
function Fun () {}
Fun.name = 'fun'
Fun.toString = function () {
    return 'This is Fun function'
}

var fun = new Fun()
fun.__proto__ === Fun.prototype  // true
```

> 1. 每个函数默认有 `prototype` 属性
> 2. 每个对象默认有 `__proto__` 属性
> 3. 对象的 `__proto__` 指向 创建该对象的函数的 `prototype`

根据这3条分析出上段代码的原型如下图：
<span style="color: #e2e2e2; font-size: 12px">(ノへ￣、)捂脸   看起来好乱……<span>

![原型链](/img/js_basic20170906/prototype.jpg)


### 面向对象

工厂模式创建的对象，类型无法被识别；

构造函数创建的对象，函数内部 this 下的方法对于每个实例来说都是重新创建的；

prototype 中的属性和方法 为 该类型的所有实例共享，因此可以将 类 共享的属性和方法添加到 prototype 对象中；

而对象属性值一般不共享，因此常用来自定义类型的方式是：构造函数与 prototype 组合使用。

```
function Animal () {
    this.name = 'animal'
    this.age = 10
}

Animal.prototype.getName = function () {
    return this.name
}
```

### 继承

js 中的继承主要是通过原型链实现的，利用原型让一个引用类型继承另一个引用类型的属性和方法。

```
function Animal () {
    this.name = 'animal'
    this.age = 10
    this.places = ['home', 'school']
}

Animal.prototype.getName = function () {
    return this.name
}

function Cat () {
    Animal.call(this)  // 解决属性为对象时父子类不能隔离的情况
    this.color = 'black'
}
Cat.prototype = new Animal()  // 实现继承
Cat.prototype.constructor = Cat   // 将 constructor 指回子类
Cat.prototype.getColor = function () {
    return this.color
}
```
 

## 参考

- [《JavaScript高级程序设计》](https://book.douban.com/subject/10546125/)

- [深入理解javascript原型和闭包](http://www.cnblogs.com/wangfupeng1988/p/3977924.html)
