title: 前端常见布局
date: 2017-03-31 17:00:00
categories: front-end
tags: layout
---


> 1. 单列布局
> 2. 多列布局

<!-- more -->



## 单列布局

<p style="text-align: center"><img src="/img/layout20170317/layout-center.png" width="300" style="" /></p>

```
<!-- html  -->
<div class="parent">
    <div class="child">This is content.</div>
</div>
```

### 水平居中

```
// 1. text-align
// 优点：兼容性好
// 不足：需要同时设置子元素和父元素
.parent {
    text-align: center;
    .child {
        display: inline-block;
    }
}

// 2. margin
// 优点：兼容性好
// 缺点: 需要指定宽度
.parent {
    border: 1px solid #03a9f4;
    .child {
        width: 120px;
        margin: 0 auto;
    }
}

// 3. table + margin
// 优点: 只需要对自身进行设置
// 不足: IE6,7需要调整结构
.parent {
    display: table;
    margin: 0 auto;
}

// 4. position
// 不足：兼容性差, IE9及以上可用
.parent {
    position: relative;
    .child {
        position: absolute;
        left: 50%;
        transform: translate(-50%);
    }
}

// 5. flex
// 缺点：兼容性差，如果进行大面积的布局可能会影响效率
.parent {
    display: flex;
    justify-content: center;

    .child {
        margin: 0 auto;
    }
}
```

### 垂直居中
```
// 1. vertical-align + table-cell
.parent {
    display: table-cell; // 宽度根据内容决定
    height: 100px;  
    vertical-align: middle;
    border: 1px solid #03a9f4;
}

// 2. vertical-align + inline-block
.parent {
    display: inline-block; // 宽度根据内容决定；若不设置此值，宽度自适应
    line-height: 200px;
    vertical-align: middle;
    border: 1px solid #03a9f4;
}

// 3. position
.parent {
    position: relative;
    height: 100px;
    border: 1px solid #03a9f4;

    .child {
        position: absolute;
        top: 50%;
        transform: translate(0, -50%);
    }
}

// 4. flex
.parent {
    display: flex;
    align-items: center;
    height: 100px;
    border: 1px solid #03a9f4;
}
```

### 水平垂直居中
```
// 1. table
.parent {
    display: table-cell;
    vertical-align: middle;
    text-align: center;
    width: 345px;
    height: 150px;
    border: 1px solid #03a9f4;

    .child {
        display: inline-block;
        border: 1px solid #00bcd4;
    }
}

// 2. position
.parent {
    position: relative;
    width: 345px;
    height: 150px;
    border: 1px solid #03a9f4;

    .child {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        border: 1px solid #00bcd4;
    }
}

// 3. flex
.parent {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 345px;
    height: 150px;
    border: 1px solid #03a9f4;

    .child {
        border: 1px solid #00bcd4;
    }
}
```

## 多列布局

<p style="text-align: center"><img src="/img/layout20170317/layout-layout.png" width="300" style="" /><img src="/img/layout20170317/layout-auto.png" width="300" style="" /></p>

### 左栏固定宽度，右栏自适应

```
<!-- html  -->
<div class="parent">
    <div class="left">left</div>
    <div class="right">
        <div class="right-fix">right</div>
    </div>
</div>

// less
.left,
.right {
    border: 1px solid #03a9f4;
}

// 1. float + margin(1)
.left {
    float: left;
    width: 100px;
}
.right {
    margin-left: 300px;
}

// 2. float + margin(2)
.left {
    width: 100px;
    float: left;
}
.right {
    float: right;
    width: 100%;
    margin-left: -100px;

    .right-fix {
        margin-left: 100px;
    }
}

// 3. float + overflow
.left {
    width: 100px;
    float: left;
}
.right {
    overflow: hidden; // 触发bfc模式，浮动无法影响，隔离其他元素，IE6不支持
}
// 左侧left设置 margin-left 当作left与right之间的边距，右侧利用overflow:hidden 进行形成bfc模式
// 如果我们需要将两列设置为等高，可以用下述方法将『背景』设置为等高，其实并不是内容的等高
.parent {
    overflow: hidden;
}
.left,
.right {
    padding-bottom: 9999px;
    margin-bottom: -9999px;
}

// 4. table
.parent {
    display: table;
    width: 100%;
    table-layout: fixed;
    .left {
        width: 100px;
    }
    .left,
    .right {
        display: table-cell;
    }
}

// 5. flex
.parent {
    display: flex;
    .left {
        width: 100px;
    }
    .right {
        flex: 1;
    }
}
```

### 右栏固定宽度，左栏自适应

```
<div class="parent">
    <div class="left">left</div>
    <div class="right">right</div>
</div>

// less
.left,
.right {
    border: 1px solid #03a9f4;
}
.right {
    width: 100px;
}

// 1. float + margin
.left {
    float: left;
    width: 100%;
    margin-right: -100px;
}
.right {
    float: right;
}

// 2. table
.parent {
    display: table;
    table-layout: fixed;
    width: 100%;
    .left,
    .right {
        display: table-cell;
    }
    .left {

    }
}

// 3. flex
.parent {
    display: flex;
    .left {
        flex: 1
    }
}
```

### 两栏固定宽度，一栏自适应

```
<div class="parent">
    <div class="left">left</div>
    <div class="center">center</div>
    <div class="right">right</div>
</div>

.left,
.center,
.right {
    border: 1px solid #03a9f4;
}

.left,
.center {
    width: 50px;
}

// 1. float
.left,
.center{
    float: left;
}
.right {
    margin-left: 100px;
}

// 2. float + overflow
.left,
.center {
    float: left;
}
.right {
    overflow: hidden;
}

// 3. table
.parent {
    display: table;
    table-layout: fixed;
    width: 100%;

    .left,
    .center,
    .right {
        display: table-cell;
    }
}

// 4. flex
.parent {
    display: flex;

    .right {
        flex: 1;
    }
}
```


### 两侧固定宽度，中栏自适应

```
<div class="parent">
    <div class="left">left</div>
    <div class="center">center</div>
    <div class="right">right</div>
</div>

// less
.left,
.center,
.right {
    border: 1px solid #03a9f4;
}

.left,
.right {
    width: 50px;
}

// 1. float
.parent {
    position: relative;
    overflow: hidden;

    .left {
        float: left;
    }
    .center {
        position: absolute;
        left: 50px;
        right: 50px;
    }
    .right {
        float: right;
    }
}

// 2. table
.parent {
    display: table;
    width: 100%;
    .left,
    .center,
    .right {
        display: table-cell;
    }
}

// 3. flex
.parent {
    display: flex;
    .center {
        flex: 1;
    }
}
```

### 一列不定宽，一列自适应

```
<div class="parent">
    <div class="left">left</div>
    <div class="right">right</div>
</div>

.left,
.right {
    border: 1px solid #03a9f4;
}

// 1. float + overflow
.parent {
    .left {
        float: left;
    }
    .right {
        overflow: hidden;
    }
}

// 2. table
.parent {
    display: table;
    table-layout: fixed;
    width: 100%;
    .left,
    .right {
        display: table-cell;
    }
    .left {
        width: 0.1%; // 黑人问号？？？
    }
}

// 3. flex
.parent {
    display: flex;
    .right {
        flex: 1;
    }
}
```


### 多列等分布局
```
<div class="parent">
    <div class="column">column</div>
    <div class="column">column</div>
    <div class="column">column</div>
    <div class="column">column</div>
</div>

// less
.column {
    background-color: #03a9f4;
    border: 1px solid #fff;
    box-sizing: border-box;
}

// 1. float
.parent {
    .column {
        float: left;
        width: 25%;
        padding-left: 20px;
    }
}

// 2. table
.parent {
    display: table;
    table-layout: fixed;
    width: 100%;
    .column {
        display: table-cell;
        padding-left: 20px;
    }
}

// 3. flex
.parent {
    display: flex;
    .column {
        flex: 1;
    }
}
```

### 九宫格布局
```
<div class="parent">
    <div class="row">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
    <div class="row">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
    <div class="row">
        <div class="item">item</div>
        <div class="item">item</div>
        <div class="item">item</div>
    </div>
</div>

// less
.item {
    background-color: #03a9f4;
    border: 1px solid #fff;
    box-sizing: border-box;
}

// 1. table
.parent {
    display: table;
    table-layout: fixed;
    width: 100%;

    .row {
        display: table-row;

        .item {
            display: table-cell;
            width: 33.3%;
        }
    }
}

// 2. flex
.parent {
    display: flex;
    flex-direction: column;

    .row {
        display: flex;
        height: 50px;

        .item {
            width: 50px;
        }
    }
}
```

### 全屏布局

```
<div class="parent">
    <div class="top">top</div>
    <div class="middle">
        <div class="left">left</div>
        <div class="right">right</div>
    </div>
    <div class="bottom">bottom</div>
</div>

// less
// 1. position
.parent {
    position: relative;
    height: 200px; // 100% 如果相对与body的话直接100%，overflow: hidden

    background-color: #03a9f4;
    .top {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        height: 30px;

        box-sizing: border-box;
        border-bottom: 1px solid #fff;
    }
    .left {
        position: absolute;
        left: 0;
        top: 30px;
        bottom: 20px;
        width: 100px;

        box-sizing: border-box;
        border-right: 1px solid #fff;
    }
    .right {
        position: absolute;
        top: 30px;
        right: 0;
        bottom: 20px;
    }
    .bottom {
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        height: 20px;

        box-sizing: border-box;
        border-top: 1px solid #fff;
    }
}

// 2. flex
.parent {
    display: flex;
    flex-direction: column;
    // height: 200px;

    background-color: #03a9f4;
    .top {

        box-sizing: border-box;
        border-bottom: 1px solid #fff;
    }
    .middle {
        display: flex;
        .left {
            width: 100px;
            box-sizing: border-box;
            border-right: 1px solid #fff;
        }
        .right {
            flex: 1;
        }
    }

    .bottom {
        box-sizing: border-box;
        border-top: 1px solid #fff;
    }
}
```
