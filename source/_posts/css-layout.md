---
title: css布局
date: 2016-09-25 21:16:01
tags: "css"
categories: "css"
---

![css布局](index.jpg)

布局（layout）即对事物的全面规划和安排，页面布局是对页面的文字、图像或表格进行格式化版式排列。网页布局对改善网站的外观非常重要，又称版式布局。
常见的布局结构分为：单列布局，两列布局，三列布局和混合布局，其中使用最多的是混合布局，即按照网站的实际需求使用多列进行布局。

<!--more-->


# 居中方案
## 水平居中
### text-align:center
 
```
.parent {
    width: 500px;
    height: 100px;
    border: 1px solid #ddd;
    text-align: center;
}

.child {
    display: inline-block;
    width: 100px;
}

<div class="parent">
    <span class="child">
        123
    </span>
</div>
```
该方法可以水平居中块级元素中的行内元素，如`inline`，`inline-block`；
对于块级元素中的块级元素，只会让子元素中的内容居中，子元素仍然是左对齐，如上述例子中span改成`display：block`，则child会左对齐，其中的文字会相对于span居中。

### margin:0 auto

```
.parent {
    width: 500px;
    height: 100px;
    border: 1px solid #ddd;
}

.child {
    display: block;
    margin:0 auto;
    width: 100px;
}

<div class="parent">
    <span class="child">
        123
    </span>
</div>
```

对于已知width的块级元素，可以用`margin：0 auto`来设置水平居中。

## 垂直居中
### line-height

对于已知height的**单行文本**，设置line-height=height的值，即可实现垂直居中。

### vertical-align: middle

模拟成表格属性来实现居中。

```
.parent {
    display: table-cell;  //模拟表格属性
    vertical-align: middle;
    text-align: center;
    width: 500px;
    height: 100px;
    border: 1px solid #ddd;
}

.child {
    display: inline-block;
    width: 100px;
}

<div class="parent">
    <span class="child">
        123
    </span>
</div>
```

### position + translate

对于height和width未知的元素可以用此方法

```
.parent {
    position: relative;
    width: 500px;
    height: 100px;
    border: 1px solid #ddd;
}

.child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #ddd;
}

<div class="parent">
    <span class="child">
        123
        <br>
        123
        <br>
        123
    </span>
</div>
```

### flex布局

对于多个元素可以用flex布局，这个可以参考阮一峰的文章 [Flex 布局教程](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool)，上面写得很详细了，这里简单举个例子：

```
.parent {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 500px;
    height: 100px;
    border: 1px solid #ddd;
}

.child {
    width: 30px;
    height: 30px;
    background: #ddd;
}

<div class="parent">
    <div class="child">1</div>
    <div class="child">2</div>
    <div class="child">3</div>
</div>
```

# 单列布局
单列布局为header、content、footer三块组成，特点是定宽、水平居中。

![单列布局](1.png)

```
.layout {
    margin: 0 auto;
    width: 200px;
}

.header{
    width:100%;
    height:50px;
    background: red;
}

.content{
    width:100%;
    height:200px;  // 实际开发时根据内容撑开，不固定高度
    background: green;
}

.footer{
    width:100%;
    height: 50px;
    background: gray;
}

<div class="layout">
    <div class="header">头部</div>
    <div class="content">内容</div>
    <div class="footer">尾部</div>
</div>
```
# 二列布局

左边定宽，右边自适应

![二列布局](2.png)

## position + margin

```
.left {
    position: absolute;
    top: 0;
    left: 0;
    width: 150px;
    background: red;
}

.right {
    margin-left: 160px;
    background: green;
}

<div class="left">左边position+定宽</div>
<div class="right">右边自适应</div>
```

## float + overflow

```
.left {
    margin-right: 10px;    // 如需设置两列之间的间距
    float: left;
    width: 150px;
    background: red;
}

.right {
    overflow: auto;
    background: gray;
}

<div class="left">左边float+定宽</div>
<div class="right">右边自适应</div>
```

# 三列布局

![三列布局](3.png)

## posotion + margin

```
.left {
    position: absolute;
    top: 0;
    left: 0;
    width: 150px;
    background: red;
}

.right {
    position: absolute;
    top: 0;
    right: 0;
    width: 150px;
    background: gray;
}

.main{
    margin:0 160px;
    background: green;
}
        
<div class="left">左边position+定宽</div>
<div class="main">中间自适应</div>
<div class="right">右边position+定宽</div>
```

## float + overflow + margin

```
.left {
    float: left;
    width: 150px;
    background: red;
}

.right {
    float: right;
    width: 150px;
    overflow: auto;
    background: gray;
}

.main {
    margin: 0 160px;
    background: green;
}
        
// 注意排列顺序为left、right、main
<div class="left">左边float+定宽</div>
<div class="right">右边float+定宽</div>
<div class="main">中间margin+宽度自适应</div>
```

# 圣杯布局

float + 负margin + padding + position

特点：两侧定宽，中间自适应

思路：
 1. 按main、left、right排序；
 2. 三者都float:left;
 3. 中间栏设置width:100%，左右两侧栏设置一定值width(如此例中的150和100)；
 4. 此时中间栏独占一行，把左右两侧栏挤下去了，设置左侧栏margin-left:-100%，将左侧栏拉上来至第一栏处；同理，设置右侧栏margin-left:-100px，将右侧栏拉上来至第三栏处；
 5. 此时会发现中间栏的头部和尾部分别被左右两栏的覆盖了，所以设置父元素padding：0 100px 0 150px，给左右两栏留出足够的空间，然后再把左右两栏相对定位至留空的地方（position:relative）;

```
.content {
    padding: 0 100px 0 150px;
    overflow: hidden;   //用于清除浮动
}

.main {
    float: left;
    width: 100%;
    background: green;
}

.left {
    float: left;
    position: relative;
    left: -150px;
    margin-left: -100%;
    width: 150px;
    background: red;
}

.right {
    float: left;
    position: relative;
    right:-100px;
    margin-left: -100px;
    width: 100px;
    background: gray;
}

<div class="content">
    <div class="main">中间自适应</div>
    <div class="left">左边定宽</div>
    <div class="right">右边定宽</div>
</div>
```

# 双飞翼布局

float + 负margin + margin

特点：两侧定宽，中间自适应

思路：类似于圣杯布局

1~4思路同圣杯布局一样；

5.不在父元素上设置padding，而是在中间栏里再添加一层div，设置margin：0 100px 0 150px，这样，中间栏的宽度就会缩小至左右两侧之间，而不会被其覆盖，省掉了对两侧栏设置相对布局的步骤;

```
.content {
    overflow: hidden;
}

.main {
    float: left;
    width: 100%;
}

.main-content {
    margin: 0 100px 0 150px;
    background: green;
}

.left {
    float: left;
    margin-left: -100%;
    width: 150px;
    background: red;
}

.right {
    float: left;
    margin-left: -100px;
    width: 100px;
    background: gray;
}

<div class="content">
    <div class="main">
        <div class="main-content">中间自适应</div>
    </div>
    <div class="left">左边定宽</div>
    <div class="right">右边定宽</div>
</div>
```


 

