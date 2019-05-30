---
title: 前端安全之XSS
date: 2019-05-30 22:20:00
tag: js
category: js

---

### XSS定义

XSS, 即为（Cross Site Scripting）, 中文名为跨站脚本, 是发生在**目标用户的浏览器**层面上的，当渲染DOM树的过程成发生了**不在预期内**执行的JS代码时，就发生了XSS攻击。

跨站脚本的重点不在‘跨站’上，而在于‘脚本’上。大多数XSS攻击的主要方式是嵌入一段远程或者第三方域上的JS代码。实际上是在目标网站的作用域下执行了这段js代码。

<!-- more -->

### XSS攻击方式

**反射型 XSS**

反射型XSS，也叫非持久型XSS，是指发生请求时，XSS代码出现在请求URL中，作为参数提交到服务器，服务器解析并响应。响应结果中包含XSS代码，最后浏览器解析并执行。

从概念上可以看出，反射型XSS代码是**首先**出现在URL中的，**然后**需要服务端解析，**最后**需要浏览器解析之后XSS代码才能够攻击。

举一个小栗子。

使用express起一个web服务器，然后设置一下请求接口。通过ajax的GET请求将参数发往服务器，服务器解析成json后响应。将返回的数据解析后显示到页面上。（没有对返回的数据进行解码和过滤等操作。）
```javascript
    html
    <textarea name="txt" id="txt" cols="80" rows="10">
    <button type="button" id="test">测试</button>
    
    js
    var test = document.querySelector('#test')
    test.addEventListener('click', function () {
      var url = `/test?test=${txt.value}`   // 1. 发送一个GET请求
      var xhr = new XMLHttpRequest()
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
          if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
            // 3. 客户端解析JSON，并执行
            var str = JSON.parse(xhr.responseText).test
            var node = `${str}`
            document.body.insertAdjacentHTML('beforeend', node)
          } else {
            console.log('error', xhr.responseText)
          }
        }
      }
      xhr.open('GET', url, true)
      xhr.send(null)
    }, false)
    
    express
    var express = require('express');
    var router = express.Router();
    
    router.get('/test', function (req, res, next) {
     // 2. 服务端解析成JSON后响应
      res.json({
        test: req.query.test
      })
    })
```

现在我们通过给textarea添加一段有攻击目的的img标签，
```javascript
    <img src="null" onerror='alert(document.cookie)' />
```
实际的页面时这样的。  
![](https://images2017.cnblogs.com/blog/896144/201710/896144-20171029192732711-518077370.png)  
ok现在，我们点击<测试>按钮，一个XSS攻击就发生了。下面图片中是获取了本地的部分cookie信息  

![pic](https://images2017.cnblogs.com/blog/896144/201710/896144-20171029192743164-1296520098.png)  

实际上，我们只是模拟攻击，通过alert获取到了个人的cookie信息。但是如果是黑客的话，他们会注入一段第三方的js代码，然后将获取到的cookie信息存到他们的服务器上。这样的话黑客们就有机会拿到我们的身份认证做一些违法的事情了。

以上，存在的一些问题，主要在于没有对用户输入的信息进行过滤，同时没有剔除掉DOM节点中存在的一些有危害的事件和一些有危害的DOM节点。

**存储型 XSS**  
存储型XSS，也叫持久型XSS，主要是将XSS代码发送到服务器（不管是数据库、内存还是文件系统等。），然后在下次请求页面的时候就不用带上XSS代码了。

最典型的就是留言板XSS。用户提交了一条包含XSS代码的留言到数据库。当目标用户查询留言时，那些留言的内容会从服务器解析之后加载出来。浏览器发现有XSS代码，就当做正常的HTML和JS解析执行。XSS攻击就发生了。  
**DOM XSS**  
DOM XSS攻击不同于反射型XSS和存储型XSS，DOM XSS代码不需要服务器端的解析响应的直接参与，而是通过浏览器端的DOM解析。这完全是客户端的事情。

DOM XSS代码的攻击发生的可能在于我们编写JS代码造成的。我们知道eval语句有一个作用是将一段字符串转换为真正的JS语句，因此在JS中使用eval是很危险的事情，容易造成XSS攻击。避免使用eval语句。

如以下代码

    test.addEventListener('click', function () {
      var node = window.eval(txt.value)
      window.alert(node)
    }, false)
    
    txt中的代码如下
    <img src='null' onerror='alert(123)' />
    

以上通过eval语句就造成了XSS攻击。

### XSS危害

1.  通过document.cookie盗取cookie
2.  使用js或css破坏页面正常的结构与样式
3.  流量劫持（通过访问某段具有window.location.href定位到其他页面）
4.  Dos攻击：利用合理的客户端请求来占用过多的服务器资源，从而使合法用户无法得到服务器响应。
5.  利用iframe、frame、XMLHttpRequest或上述Flash等方式，以（被攻击）用户的身份执行一些管理动作，或执行一些一般的如发微博、加好友、发私信等操作。
6.  利用可被攻击的域受到其他域信任的特点，以受信任来源的身份请求一些平时不允许的操作，如进行不当的投票活动。

### XSS防御

从以上的反射型和DOM XSS攻击可以看出，我们不能原样的将用户输入的数据直接存到服务器，需要对数据进行一些处理。以上的代码出现的一些问题如下

1.  没有过滤危险的DOM节点。如具有执行脚本能力的script, 具有显示广告和色情图片的img, 具有改变样式的link, style, 具有内嵌页面的iframe, frame等元素节点。
2.  没有过滤危险的属性节点。如事件, style, src, href等
3.  没有对cookie设置httpOnly。

如果将以上三点都在渲染过程中过滤，那么出现的XSS攻击的概率也就小很多。

解决方法如下

**对cookie的保护**

1.  对重要的cookie设置httpOnly, 防止客户端通过`document.cookie`读取cookie。服务端可以设置此字段。

**对用户输入数据的处理**

1.  编码：不能对用户输入的内容都保持原样，对用户输入的数据进行字符实体编码。对于字符实体的概念可以参考文章底部给出的参考链接。
2.  解码：原样显示内容的时候必须解码，不然显示不到内容了。
3.  过滤：把输入的一些不合法的东西都过滤掉，从而保证安全性。如移除用户上传的DOM属性，如onerror，移除用户上传的Style节点，iframe, script节点等。

通过一个例子讲解一下如何处理用户输入的数据。

实现原理如下：

1.  存在一个parse函数，对输入的数据进行处理，返回处理之后的数据
2.  对输入的数据（如DOM节点）进行解码（使用第三方库 he.js）
3.  过滤掉一些元素有危害的元素节点与属性节点。如script标签，onerror事件等。（使用第三方库HTMLParser.js）
```javascript
    <script src='/javascripts/htmlparse.js'></script>
    <script src='/javascripts/he.js'></script>
    // 第三方库资源在文章底部给出
    
    // parse函数实现如下
    
    function parse (str) {
          // str假如为某个DOM字符串
          // 1. result为处理之后的DOM节点
          let result = ''
          // 2. 解码
          let decode = he.unescape(str, {
              strict: true
          })
          HTMLParser(decode, {
              start (tag, attrs, unary) {
                  // 3. 过滤常见危险的标签
                  if (tag === 'script' || tag === 'img' || tag === 'link' || tag === 'style' || tag === 'iframe' || tag === 'frame') return
                  result += `<${tag}`
                  for (let i = 0; i < attrs.length; i++) {
                      let name = (attrs[i].name).toLowerCase()
                      let value = attrs[i].escaped
                      // 3. 过滤掉危险的style属性和js事件
                      if (name === 'style' || name === 'href' || name === 'src' || ~name.indexOf('on')) continue
                      result += ` ${name}=${value}`
                  }
                  result += `${unary ? ' /' : ''} >`
              },
              chars (text) {
                  result += text
              },
              comment (text) {
                  result += `<!-- ${text} -->`
              },
              end (tag) {
                  result += `</${tag}>`
              }
          })
          return result
      }
```

因此，有了以上的parse函数之后，就可以避免大部分的xss攻击了。
```javascript
    test.addEventListener('click', function () {
      // ... 省略部分代码
      xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
          if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
            // 3. 客户端解析JSON，并执行
            // test按钮的点击事件中唯一的变化就是使用parse对服务端返回的数据进行了解码和过滤的处理。
            var str = parse(JSON.parse(xhr.responseText).test)
            // 通过parse解析之后返回的数据就是安全的DOM字符串
            var node = `${str}`
            document.body.insertAdjacentHTML('beforeend', node)
          }
        }
      }
      // ... 省略部分代码
    }, false)
```
那么，栗子说完了。

稍微总结一下

1.  一旦在DOM解析过程成出现不在预期内的改变（JS代码执行或样式大量变化时），就可能发生XSS攻击
2.  XSS分为反射型XSS，存储型XSS和DOM XSS
3.  反射型XSS是在将XSS代码放在URL中，将参数提交到服务器。服务器解析后响应，在响应结果中存在XSS代码，最终通过浏览器解析执行。
4.  存储型XSS是将XSS代码存储到服务端（数据库、内存、文件系统等），在下次请求同一个页面时就不需要带上XSS代码了，而是从服务器读取。
5.  DOM XSS的发生主要是在JS中使用eval造成的，所以应当避免使用eval语句。
6.  XSS危害有盗取用户cookie，通过JS或CSS改变样式，DDos造成正常用户无法得到服务器响应。
7.  XSS代码的预防主要通过对数据解码，再过滤掉危险标签、属性和事件等。

**参考资源**

1.  《WEB前端黑客技术揭秘》
2.  [浅谈XSS攻击的那些事（附常用绕过姿势)](https://zhuanlan.zhihu.com/p/26177815)
3.  [XSS实战：我是如何拿下你的百度账号](https://zhuanlan.zhihu.com/p/24249045)
4.  [HTMLParser](https://github.com/blowsie/Pure-JavaScript-HTML5-Parser)
5.  [he](https://github.com/mathiasbynens/he)
6.  [Web安全-XSS](http://www.imooc.com/learn/812)