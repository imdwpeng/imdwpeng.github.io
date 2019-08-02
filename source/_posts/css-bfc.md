---
title: 【CSS】BFC原理及应用解析
date: 2017-08-16 14:34:16
tags: "css"
categories: "css"
---

![css-bfc](index.jpeg)

<!--more-->

## 什么是BFC
第一次听到这个名字，我是一脸懵逼的，css 什么时候有这东西了？于是偷偷上网脑补一下，原来这就是个隐藏的大boss，我们每时每刻都在用着，却不知道原来这就是它的庐山真面目。
**BFC**（Block Formatting Context），中文直译为块级格式上下文。它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用。简单来说，它就是一个独立的盒子，这个盒子中的元素的布局与盒子之外的布局之间不会相互影响。

## 创建 BFC

 1. 浮动元素：`float`属性不为`none`
 2. `overflow`不为`visible`(可以是`hidden`、`scroll`、`auto`)
 3. 绝对定位元素：`position`为`absolute`或`fixed`
 4. `display`为`inline-block`、`table-cell`、`table-caption`

## BFC 布局规则
1.在`BFC`下，内部的块级元素会从顶部开始挨个的垂直排列，水平方向撑满整个宽度（除非该元素本身又创建了新的`BFC`）；
2.每个块级元素的左边框都是紧挨着盒子的左外边的，即使存在浮动也一样，（从右到左的格式，则是挨着右外边）；
![bfc-1](1.png)

3.两个`BFC`之间的间距是根据`margin`值决定的，这个是正常的规则，但是属于一个`BFC`下的块级元素之间的`margin`则会发生重叠，它们是由`margin`值大的一方主宰； 
![bfc-2](2.png)

```
.parent {
    margin: 10px 0;
    border: 1px solid #000;
    background: red;
}

.child {
    margin: 10px 0;
    height: 24px;
    background: green;
}

.new-bfc{
    overflow: hidden;
}

<div class="parent">
    <div class="child">Child 1</div>
    <div class="child">Child 2</div>
    <div class="child">Child 3</div>
</div>
<div class="parent">
    <div class="child">Child 1</div>
    <div class="child">Child 2</div>
    <div class="new-bfc">      //新建一个BFC
        <div class="child">Child 3</div>
    </div>
</div>
```
4.计算`BFC`的高度时，浮动元素也参与计算，看到这条规则，你能联想到什么？没错，这也是一种 **清除浮动** 的有效方法；
```
.parent {
    width: 120px;
    border: 1px solid #000;
}

.child {
    float: left;
    margin: 10px;
    width: 100px;
    height: 24px;
    background: green;
}

<div class="parent">
    <div class="child">Child</div>
</div>
```
在上面的这个例子中，父容器没有设置高度，子元素由于浮动不能撑开父元素的高度，所以出现父元素高度塌陷问题。为了解决这个问题，我们通过添加`overflow:hidden`，在容器中创建一个新的BFC。修改过父元素的CSS：
```
.parent {
    width: 120px;
    border: 1px solid #000;
    overflow: hidden;
}
```
此时，计算父元素的高度时，浮动元素也会参与计算，解决了高度塌陷的问题。

## BFC 应用
### 解决浮动
这个上面讲过了，这里不再重复。

### 自适应两栏布局
采用`float + overflow:hidden`布局
```
.parent {
    width: 250px;
    border: 1px solid #000;
}

.aside {
    float: left;
    margin: 10px;
    width: 100px;
    height: 25px;
    background: green;
}

.main {
    height: 50px;
    background: red;
    overflow: hidden;
}

<div class="parent">
    <div class="aside">aside</div>
    <div class="main">main</div>
</div>
```
aside模块设置`float:left`，main模块设置`overflow:hidden`，两者都是`BFC`，所以相互独立，互不影响。
<footer>
<hr/>
![footer](https://raw.githubusercontent.com/imdwpeng/photoGallery/master/footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>

