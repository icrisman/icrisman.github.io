---
title: 前端规范
date: 2016-08-30 22:00:31
categories: front-end
tags: 规范
---

前端项目的各种规范对于一个本着对项目负责对自己的代码负责的前端er来说，是必不可少的。
以下整理一些基础的规范，供参考。
*当别人接手到你的项目，觉得一切都特别清晰的时候，你可以自豪的说这是哥写的~！*

> - [项目目录结构](#dir)
> - [开发和部署目录结构](#src_asset_dir)
> - [命名规范](#naming_spec)
>   - 目录命名规范
>   - 文件命名规范
>   - className 命名规范

<!-- more -->

------

## <h3 id="dir">项目目录结构</h3>

包括但不限于以下内容：
```
${root}/
    web/
        src/
            ...
        asset/
            ...
        dep/
        build/
        test/
        doc/
        package.json
        README.md
```

* \${root}/
${root} 为项目名称。

* web/
web 目录为该项目下的前端模块，如果仅包括前端模块，该层可省略。

* src/
前端开发目录。

* asset/
前端部署目录。

* dep/
用于存放依赖的第三方包，不允许做任何改动。
> 第三方依赖通过 `npm` 安装时，默认安装在 `node_modules` 目录下，无须做改动，部署时配置好部署规则即可。

* build/
用于存放工具类脚本。

* test/
用于存放测试用例以及开发阶段的模拟数据。

* doc/
用于存放项目文档。

* package.json
项目描述文件。
> `npm init` 可生成该文件
> `npm install jquery --save-dev` 安装依赖时可自动生成依赖声明

* README.md
项目描述文件，概要描述此项目的功能等信息。


## <h3 id="src_asset_dir">开发和部署目录结构</h3>

### 开发目录 src/

开发目录按功能划分。
`src/` 下首先按功能划分目录，包括 `通用子模块common` 和 `业务子模块`。子模块目录下的静态资源js、css、html可不划分目录，如文件过多可考虑继续拆分子模块。
如果项目为 SPA ，可将页面入口文件直接放在 `web/` 目录下，如果有多个入口文件，可在src/ 目录同级添加 `entry` 目录。

```
src/
    common/
        img/
            sprites.png
            logo.png
        conf.js
        layout.tpl.html
        layout.less
    biz1/
        img/
            add_button.png
        add.js
        add.tpl.html
        add.less
    biz2/
        subbiz1/
            list.js
            list.tpl.html
            list.css
        subbiz2/
entry/
    index.html
    main.html
```


### 部署目录 asset/

通常构建工具会对 `src/`、 `dep/` 等目录下的资源进行分析、合并与压缩等，生成到asset目录下。

`asset/` 目录下一般有两个目录：

* static/: 静态资源如js、css、img等，按照 src/ 下的结构
* tpl/: 模板文件，按照 src/ 下的结构

```
${root}/
    web/
        asset/
            static/
                ...
            tpl/
                ...
```

## <h3 id="naming_spec">命名规范</h3>

### 目录命名规范

目录按功能命名，一般用一个单词，小写，不宜过长；如需用多个单词，用下划线作为连接符。
模块内的图片统一放在 `img/` 目录。


### 文件命名规范

文件命名以字母开头，且一律小写，以下划线作为多个单词的连接符，且只包含字母、下划线、数字。

> 同一个模块的 js、css、html 可使用相同的命名，fis3 可配置开启同名依赖(useSameNameRequire)。

#### 1. js 文件命名

根据使用框架的不同，js文件可能存在多种用途，因此可按照命名后缀区分其用途，不在此列的按普通文件命名。

- {module_name}_ctrl.js
mv* 框架中的 controller，未使用 mv* 框架时不做区分。

- {module_name}_service.js
对于与后端交互的接口操作可抽象为服务层。


#### 2. img 文件命名

图片命名建议按以下顺序命名：
业务模块名称（可选） + （mod_）图片功能类别（必选）+ 图片模块名称（可选） + 图片精度（可选）

- 图片功能类别
mod_：是否公共，可选
icon：图标
logo：LOGO类
spr：单页面各种元素合并集合
btn：按钮
bg：可平铺或者大背景

- 图片精度
普清：@1x
Retina：@2x | @3x

### className 命名规范

ClassName的命名应该尽量精短、明确，必须以字母开头命名，且全部字母为小写，单词之间统一使用中划线 “-” 连接

#### 命名原则
基于姓氏命名法（继承 + 外来）。
祖先模块不能出现下划线，除了是全站公用模块，如 mod_ 系列的命名：

**推荐：**

```
<div class="modulename">
    <div class="modulename_info">
        <div class="modulename_son"></div>
        <div class="modulename_son"></div>
        ...
    </div>
</div>
    
<!-- 这个是全站公用模块，祖先模块允许直接出现下划线 -->
<div class="mod_info">
    <div class="mod_info_son"></div>
    <div class="mod_info_son"></div>
    ...     
</div>
```

**不推荐：**

```
<div class="modulename_info">
    <div class="modulename_info_son"></div>
    <div class="modulename_info_son"></div>
    ...     
</div>
```

在子孙模块数量可预测的情况下，严格继承祖先模块的命名前缀

```
<div class="modulename">
    <div class="modulename_cover"></div>
    <div class="modulename_info"></div>
</div>
```

当子孙模块超过4级或以上的时候，可以考虑在祖先模块内具有识辨性的独立缩写作为新的子孙模块

**推荐：**

```
<div class="modulename">
    <div class="modulename_cover"></div>
    <div class="modulename_info">
        <div class="modulename_info_user">
            <div class="modulename_info_user_img">
                <!-- 这个时候 miui 为 modulename_info_user_img 首字母缩写-->
                <div class="miui_tit"></div>
                <div class="miui_txt"></div>
                ...
            </div>
        </div>
        <div class="modulename_info_list"></div>
    </div>
</div>
```

**不推荐：**

```
<div class="modulename">
    <div class="modulename_cover"></div>
    <div class="modulename_info">
        <div class="modulename_info_user">
            <div class="modulename_info_user_img">
                <div class="modulename_info_user_img_tit"></div>
                <div class="modulename_info_user_img_txt"></div>
                ...
            </div>
        </div>
        <div class="modulename_info_list"></div>
    </div>
</div>
```

#### 模块命名

全站公共模块：以 mod_ 开头

```<div class="mod_yours"></div>```

业务公共模块：以 业务名_mod_ 开头

```<div class="wx_mod_yours"></div>```

#### 常用命名推荐

注意：ad、banner、gg、guanggao 等有机会和广告挂勾的字眠不建议直接用来做ClassName，因为有些浏览器插件（Chrome的广告拦截插件等）会直接过滤这些类名，因此

```<div class="ad"></div>```

这种广告的英文或拼音类名不应该出现。
另外，敏感不和谐字眼也不应该出现，如 fuck、ass 等。

| ClassName | 含义 |
| ---- | ---- |
| about | 关于 |
| account | 账户 |
| arrow | 箭头图标 |
| article | 文章 |
| aside | 边栏 |
| audio | 音频 |
| avatar | 头像 |
| bg,background | 背景 |
| bar | 栏（工具类） |
| branding | 品牌化 |
| crumb,breadcrumbs | 面包屑 |
| btn,button | 按钮 |
| caption | 标题，说明 |
| category | 分类 |
| chart | 图表 |
| clearfix | 清除浮动 |
| close | 关闭 |
| col,column | 列 |
| comment | 评论 |
| community | 社区 |
| container | 容器 |
| content | 内容 |
| copyright | 版权 |
| current | 当前态，选中态 |
| default | 默认 |
| description | 描述 |
| details | 细节 |
| disabled | 不可用 |
| entry | 文章，博文 |
| error | 错误 |
| even | 偶数，常用于多行列表或表格中 |
| fail | 失败（提示） |
| feature | 专题 |
| fewer | 收起 |
| field | 用于表单的输入区域 |
| figure | 图 |
| filter | 筛选 |
| first | 第一个，常用于列表中 |
| footer | 页脚 |
| forum | 论坛 |
| gallery | 画廊 |
| group | 模块，清除浮动 |
| header | 页头 |
| help | 帮助 |
| hide | 隐藏 |
| hightlight | 高亮 |
| home | 主页 |
| icon | 图标 |
| info,information | 信息 |
| last | 最后一个，常用于列表中 |
| links | 链接 |
| login | 登录 |
| logout | 退出 |
| logo | 标志 |
| main | 主体 |
| menu | 菜单 |
| meta | 作者、更新时间等信息栏，一般位于标题之下 |
| module | 模块 |
| more | 更多（展开） |
| msg,message | 消息 |
| nav,navigation | 导航 |
| next | 下一页 |
| nub | 小块 |
| odd | 奇数，常用于多行列表或表格中 |
| off | 鼠标离开 |
| on | 鼠标移过 |
| output | 输出 |
| pagination | 分页 |
| pop,popup | 弹窗 |
| preview | 预览 |
| previous | 上一页 |
| primary | 主要 |
| progress | 进度条 |
| promotion | 促销 |
| rcommd,recommendations | 推荐 |
| reg,register | 注册 |
| save | 保存 |
| search | 搜索 |
| secondary | 次要 |
| section | 区块 |
| selected | 已选 |
| share | 分享 |
| show | 显示 |
| sidebar | 边栏，侧栏 |
| slide | 幻灯片，图片切换 |
| sort | 排序 |
| sub | 次级的，子级的 |
| submit | 提交 |
| subscribe | 订阅 |
| subtitle | 副标题 |
| success | 成功（提示） |
| summary | 摘要 |
| tab | 标签页 |
| table | 表格 |
| txt,text | 文本 |
| thumbnail | 缩略图 |
| time | 时间 |
| tips | 提示 |
| title | 标题 |
| video | 视频 |
| wrap,wrapper | 容器，包，一般用于最外层 |


---

部分参考[【凹凸实验室】](https://guide.aotu.io/docs/name/classname.html)
