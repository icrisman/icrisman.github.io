---
title: html5 - meta/link 标签详解
date: 2018-1-2 19:07:06
categories: front-end
tags: meta
toc: true
---

html5 meta/link 标签详解。

<!-- more -->
 
<!-- toc -->

## meta

meta标签有三个属性： char-set / name / http-equiv 

### char-set
设置字符集

```
<meta char-set="utf-8">
```

### name
用于描述网页，与属性 `content` 搭配使用，`content` 中的内容主要是便于搜索引擎机器人查找信息和分类信息用的。

- keywords 关键字
- description 网站内容描述
- robots 机器人向导
	
    content 可选值：all, none, index, noindex, follow, nofollow，默认 all。
- author 作者
    ```
    <meta name="author" contect="name, xxx@163.com" />
    ```
- generator 信息参数，代表网站用什么软件做的
- COPYRIGHT 版权信息
- revisit-after  网站重访，如’7days’代表7天。

- viewport
	
    - width 
        
        viewport的宽度[device-width | pixel_value]， width如果直接设置pixel_value 数值，大部分的安卓手机不支持，但是ios支持；
	
    - height
        
        viewport 的高度 （范围从 223 到 10,000 ）

    - user-scalable  [yes | no]是否允许缩放
    - initial-scale -  [数值] 初始化比例（范围从 > 0 到 10）
    - minimum-scale -  [数值] 允许缩放的最小比例
    - maximum-scale -  [数值] 允许缩放的最大比例
    - target-densitydpi - 值有以下（一般推荐设置中等响度密度或者低像素密度，后者设置具体的值
    - dpi_value，另外webkit内核已不准备再支持此属性）
    - dpi_value 一般是70-400//没英寸像素点的个数
    - device-dpi设备默认像素密度
    - high-dpi 高像素密度
    - medium-dpi 中等像素密度
    - low-dpi 低像素密度

- format-detection 忽略电话号码和邮箱
	```
    <meta name="format-detection" content="telephone=no; email=no”>
    ```

- renderer
	启用360浏览器的极速模式。
    ```
	<meta name="renderer" content="webkit">
    ```

- HandheldFriendly

    针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓
	```
    <meta name="HandheldFriendly" content="true”>
    ```

- MobileOptimized
	
    微软的老式浏览器。
	```
    <meta name="MobileOptimized" content="320">
    ```

    ```
    <!-- 其它 -->
    <!-- UC强制竖屏 -->
    <meta name="screen-orientation" content="portrait">
    <!-- UC强制全屏 -->
    <meta name="full-screen" content="yes">
    <!-- UC应用模式 -->
    <meta name="browsermode" content="application">
    <!-- QQ强制竖屏 -->
    <meta name="x5-orientation" content="portrait">
    <!-- QQ强制全屏 -->
    <meta name="x5-fullscreen" content="true">
    <!-- QQ应用模式 -->
    <meta name="x5-page-mode" content="app">
    <!-- sns 社交标签 begin -->
    <!— 参考微博API -->
    <meta property="og:type" content="类型" />
    <meta property="og:url" content="URL地址" />
    <meta property="og:title" content="标题" />
    <meta property="og:image" content="图片" />
    <meta property="og:description" content="描述" />
    <!-- sns 社交标签 end -->

    <!-- Windows 8 磁贴颜色 -->
    <meta name="msapplication-TileColor" content="#000"/>
    <!-- Windows 8 磁贴图标 -->
    <meta name="msapplication-TileImage" content="icon.png"/>
    <!-- windows phone 点击无高光 -->
    <meta name="msapplication-tap-highlight" content="no">
    ```

- apple-mobile-web-app-capable
	
    网站开启对web app程序的支持，其实意思就是删除默认的苹果工具栏和菜单栏，开启全屏显示
	```
    <meta name="apple-mobile-web-app-capable" content="yes">
    ```

- apple-mobile-web-app-status-bar-style
	
    在 web app 应用下状态条（屏幕顶部条）的颜色；默认值为 default（白色），可以定为 black（黑色）和 black-translucent（灰色半透明）。
	```
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    ```

- apple-mobile-web-app-title  设置桌面图标标题
	```
    <meta name="apple-mobile-web-app-title" content=“六个字内最佳”>
    ```

- apple-itunes-app  只能 App 广告条
	```
    <meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL"> 
    ``` 

### http-equiv
	
    表示 http 的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确的显示网页内容。与属性 content 搭配使用，表示各种参数的变量值。

- expires 过期时间
	
    content 值为 GMT 格式
    ```
	<meta http-equiv=“expires” content=“Fri,12Jan200118:18:18GMT”>
    ```

- pragma  
	
    cache 模式，禁止浏览器从本地缓存中访问页面内容
	```
    <meta http-equiv=“pragma” content=“no-cache”>
    ```

- refresh 自动刷新
	
    自动刷新并跳转到新页面，content 格式为秒数 + url，意为多少秒后跳转到 url。
	```
    <meta http-equiv=“refresh” content=“2;http://www.baidu.com”>
    ```

- set-cookie  cookie的设定
	
    如果网页过期，则cookie 将被删除。
	```
    <meta http-equiv="Set-Cookie"content="cookie value=xxx; expires=Friday,12-Jan-200118:18:18GMT；path=/"> 
    ```

- Window-target 显示窗口的设定
	
    强制页面在当前窗口以独立页面显示，如 _top 用来防止别人在框架里调用自己的页面。
    ```
	<meta http-equiv="Window-target"content="_top">
    ``` 

- content-type 显示字符集的设定

    ```
	<meta http-equiv="content-Type"content="text/html;charset=gb2312"> 
    ```

- content-language 显示语言的设定
	```
    <meta http-equiv="Content-Language"content="zh-cn"/> 
    ````

- cache-control 

    指定响应和请求遵循的缓存机制
	请求时的缓存指令包括no-cache、no-store、max-age、max-stale、min-fresh、only-if-cached，响应消息中的指令包括public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age
	各个消息中的指令含义如下：
	- Public - 指示响应可被任何缓存区缓存
	- Private - 指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当用户的部分响应消息，此响应消息对于其他用户的请求无效
	- no-cache - 指示请求或响应消息不能缓存
	- no-store - 用于防止重要的信息被无意的发布。在请求消息中发送将使得请求和响应消息都不使用缓存。
	- max-age - 指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应
	- min-fresh - 指示客户机可以接收响应时间小于当前时间加上指定时间的响应
	- max-stale - 指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。


- Imagetoolbar
	
    是否显示图片工具栏，content 值为 true 或 false。

- Content-script-type 指明页面中脚本类型
	```
    <meta http-equiv="Content-Language"content="zh-cn"/> 
    ```


- X-UA-Compatible
	优先使用 IE 最新版本和 chrome
    ```
	<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/>
    ```



## Link

- 设置“添加到主屏幕图标  apple-touch-icon-precomposed
    ```
	<!-- iOS 图标 begin -->  
	<link rel="apple-touch-icon-precomposed" href="/apple-touch-icon-57x57-precomposed.png"/>  

	 <!-- iPhone 和 iTouch，默认 57x57 像素，必须有 -->  
	<link rel="apple-touch-icon-precomposed" sizes="114x114" href="/apple-touch-icon-114x114-precomposed.png"/> 
     
	<!-- Retina iPhone 和 Retina iTouch，114x114 像素，可以没有，但推荐有 -->  
	<link rel="apple-touch-icon-precomposed" sizes="144x144" href="/apple-touch-icon-144x144-precomposed.png"/>  

	<!-- Retina iPad，144x144 像素，可以没有，但推荐有 -->  
	<!-- iOS 图标 end -->
    ```

- 添加 RSS 订阅
    
    ```
    <link rel="alternate" type="application/rss+xml" title="RSS" href="/rss.xml"/>
    ```

- 添加 favicon icon
    ```
    <link rel="shortcut icon" type="image/ico" href="/favicon.ico" />
    ```

- 设置启动画面
    ```
	<link rel="apple-touch-startup-image" href="Startup.png" /> 
    ```


参考:

[HTML5 meta 全解]( http://blog.csdn.net/kongjiea/article/details/17092413)