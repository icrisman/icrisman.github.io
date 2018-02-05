---
title: JS 基础 (下)
date: 2017-09-13 23:52:07
categories: front-end
tags: javascript
toc: true
---

<div style="text-align: center">-·-·-·-·-·-·-·-·-· agenda ·-·-·-·-·-·-·-·-·-</div>

<!-- toc -->

<!-- more -->

<div style="text-align: center">-·-·-·-·-·-·-·-·-· Let's start ·-·-·-·-·-·-·-·-·-</div>



## 事件

### 事件流

事件流描述的是从页面中接收事件的顺序。

- 事件冒泡：IE8及以下
- 事件捕获：IE9及以上、其它主流浏览器

DOM事件流：事件捕获阶段、处于目标阶段、事件冒泡阶段  （IE8及以下不支持

### 事件处理程序

处理事件的函数叫做事件处理程序。

- HTML 事件处理程序
```
<input type="button" id="myBtn" value="Click Me" onclick="alert('hello world')">
```

- DOM0级 事件处理程序
```
var btn = document.getElementById('myBtn')
btn.onclick = function () {
    alert('Clicked')
}
```

- DOM2级 事件处理程序

    可以添加多个，按添加的顺序执行。
    ```
    var btn = document.getElementById('myBtn') btn.addEventListener('click', function () {
        alert(this.id)
    }, false)
    ```

- IE 事件处理程序

    可以添加多个，按相反的顺序执行。
    ```
    btn.attachEvent('onclick', function () {
        alert('Clicked')
    })
    ```

- 跨浏览器的事件处理程序

    使用能力检测来判断。

### 事件对象

事件处理程序默认的参数叫做事件对象 event。

#### 事件对象的属性、方法

- DOM 中（通过事件处理程序的参数获得）：
    - `bubbles`  事件是否冒泡
    - `cancleable`    是否可以取消事件的默认行为
    - `defaultPrevented`  为 true 表示已经调用了`preventDefault()` DOM3级事件中新增
    - `trusted`  为 true 表示浏览器生成的，为 false 表示 js 触发的
    - `type`    事件类型

    - `currentTarget`     当前正在处理事件的那个元素，this 始终等于它
    - `target`   事件的目标
    - `preventDefault()`    取消事件的默认行为，cancleable 为 true 时可使用
    - `stopPropagation()`   取消事件的进一步捕获或冒泡。bubbles 为 true时可使用
- IE 中 （通过`window.event 获得`）：
    - `cancleBubble`    默认 false。与`stopPropagation()`作用相同
    - `returnValue`     默认 false。与`preventDefault()`作用相同
    - `type`    与`target`作用相同
    - `srcElement`

### 事件类型
1. UI 事件
    - `load` document window img上可触发
    - `unload`
    - `resize` window 上触发
    - `scroll` window 上触发
    - `select` `<select>`上触发
2. 焦点事件
    - `blur`
    - `focus`
    - `focusin`  <==> focus
    - `focusout`  <==> blur
3. 鼠标与滚轮事件
    - `click`
    - `dblclick`  触发两次 click 事件
    - `mouseenter`  不冒泡
    - `mousemove`
    - `mouseover`
    - `mousedown`
    - `mouseup`   与 mousedown 相继触发后才会触发 click
    - `mouseleave`  不冒泡
4. 键盘与文本事件
    - `keydown`  按下任意键触发
    - `keypress`  按下字符键时触发
    - `keyup`
5. 复合事件

    （基本用不到……
    - `compositionstart`
    - `compositionupdate`
    - `compositionend`
6. 变动事件
7. HTML5事件
    - `contextmenu` 右击显示上下文菜单
    - `beforeunload`
    - `DOMContentLoaded`  页面的 DOM 树形成后触发，一般早于 `load` 事件
    - `readystatechange`
    - `pageshow`/`pagehide`  ff和 opera 支持前进/后退时使用往返缓存`bfcache`。pageshow 在load 事件后触发，pagehide 在 unload 事件前触发。
    - `hashchange`
8. 设备事件
    - `orientationchange`  通过`window.orientation`获取相关信息
    - `deviceorientation`  设备方向改变时触发
    - `devicemotion`
9. 触摸与手势事件
    - 触摸事件
        - `touchstart`
        - `touchmove`
        - `touchend`
        - `touchcancle`
    - 手势事件：只有两个手指都触摸到事件的接收容器时才会触发这些事件
        - `gesturestart`
        - `gesturechange`
        - `gestureend`

## DOM

- Node

    节点类型，js 中所有的节点类型都继承自 Node 类型。
    - nodeType  节点类型，为常量
        - Node.ELEMENT_NODE(1)
        - Node.ATTRIBUTE_NODE(2)
        - Node.TEXT_NODE(3)
        - Node.CDATA_SECTION_NODE(4)
        - Node.ENTITY_REFERENCE_NODE(5)
        - Node.ENTITY_NODE(6)
        - Node.PROCESSING_INSTRUCTION_NODE(7)
        - Node.COMMENT_NODE(8)
        - Node.DOCUMENT_NODE(9)
        - Node.DOCUMENT_TYPE_NODE(10)
        - Node.DOCUMENT_FRAGMENT_NODE(11)
        - Node.NOTATION_NODE(12)
    - nodeName  
    - nodeValue
    - childNodes  子节点，为 NodeList 对象（类 Array 对象）
    - parentNode
    - previousSibling / nextSibling
    - firstChild / lastChild
    - hasChildNodes()
    - ownerDocument 指向表示整个文档的文档节点

    - appendChild(node) 返回新增的节点，如果参数 node 已是文档的一部分，则从旧位置移动到新位置。
    - insertBefore(toAppendNode, referNode)  referNode为参照节点，如为 null，结果同 appendChild。
    - replaceChild(newNode, toReplacedNode)
    - cloneNode(isDeep)
    - normalize()  处理文本节点，空则删除，多个合并为一个

- Document

    文档类型。document 对象是 HTMLDocument (继承自Document) 的实例，它是 window 对象的一个属性。

    - 访问子节点:
        - document.documentElement  
        - document.childNodes  (一般有两个值，一个 doctype，一个 html)

    - 文档信息
        - title
        - URL
        - domain
        - referrer

- Element

    - getAttribute('attrName')
    - setAttribute('attrName', 'value')
    - removeAttribute('attrName')

    - createElement()

- Text

    - 操作节点中的文本
        - appendData('text')
        - deleteData(offset, count)  从 offset 位置开始删除 count 个字符
        - insertData(offset, text)
        - replaceData(offset, count, text)
        - splitText(offset) 从 offset 位置将当前文本节点分隔为两个文本节点

    - createTextNode('text')

## DOM 扩展

- selectors API
    - querySelector()
    - querySelectorAll()
    - matchesSelector()
- html5
    - getElementsByClassName()
    - classList 返回元素的 class list（类 Array 对象）
        - add('className')
        - contains('className')
        - remove('className')
        - toggle('className')
    - document.activeElement  返回当前获得焦点的元素
    - HTMLDocument
        - document.readyState  返回 `loading` or  `complete`
        - document.compatMode  返回 `CSS1Compat`（标准模式） or `BackCompat`（混杂模式）
        - document.head  类似 document.body
    - document.charset / document.defaultCharset
    - data-x 自定义数据
        - dataset 访问自定义属性
    - 插入标记
        - innerHTML
        - outerHTML
        - insertAdjacentHTML('beforebegin', 'elementHTML')
            - 第一个参数必须是： beforebegin  beforeend  afterbegin  afterend

    >  在使用 innerHTML、 outerHTML 属性和 insertAdjacentHTML()方法时，最好先手工删除要被替换的元素的所有事件处理 程序和 JavaScript 对象属性

## BOM

- window

    js中的全局对象。
    - 窗口位置

        浏览器窗口相对于屏幕左/上边的距离：

        window.screenLeft / screenX

        window.screenTop / screenY
    - 窗口大小

        页面视图区的大小：
        window.innerHeight / innerWidth

        浏览器窗口本身的大小：
        window.outerHeight / outerWidth

        页面视口的大小：
        document.body.clientHeight / clientWidth

        布局视口的大小：
        document.documentElement.clientHeight / clientWidth

        屏幕的像素宽 / 高度：
        screen.width / height

- location

    与当前 url 相关的一些信息。window.location 与 document.location指向同一个对象。
    - hash
    - host  服务器名称+端口号
    - hostname  服务器名称
    - port  端口号
    - href
    - pathname
    - protocol
    - search

    - location.replace()
    - location.assign()

- navigator

    - userAgent
- screen

    保存于客户端显示器相关的一些信息。

- history
    - history.go()

        参数：`正负数`表示前进 or 后退，`字符串`表示跳转的 url
    - history.back()    后退一页
    - history.forward()     前进一页

 
