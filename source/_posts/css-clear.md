---
title: 【css】常见的清除浮动方法
date: 2016-08-03 22:17:00
tags: "css"
categories: "css"
---

当元素设置float浮动后，该元素就会脱离文档流并向左/向右浮动，直到碰到父元素或者另一个浮动元素。

浮动元素会造成父元素高度塌陷，所以当我们设置float后，需进行相应的清除浮动操作。
![未清除浮动](1.png)
<div style="text-align:center;">高度塌陷的父元素（红色区域）</div>
![清除浮动](2.png)
<div style="text-align:center;">清除浮动后的效果</div>
<!--more-->

# 使用额外的标签clear:both

```
.parent {
    padding: 10px;
    width: 200px;
    background: red;
}

.child {
    float: left;
    margin-right: 10px;
    width: 50px;
    height: 50px;
    background: green;
}

.clear{
    clear: both;
}

<div class="parent">
    <div class="child">child</div>
    <div class="child">child</div>
    <div class="child">child</div>
    <div class="clear"></div>
</div>
```

原理：在浮动元素下面添加一个空标签，在这个标签中设置clear：both；
优点：简单，浏览器兼容性好；
缺点：增加页面的标签，造成页面混乱；

# 使用overflow属性

```
.parent {
    padding: 10px;
    background: red;
    overflow: hidden;
}

.child {
    float: left;
    margin-right: 10px;
    width: 50px;
    height: 50px;
    background: green;
}

<div class="parent">
    <div class="child">child</div>
    <div class="child">child</div>
    <div class="child">child</div>
</div>
```

原理：父元素定义overflow:hidden，此时，浏览器会自动检查浮动区域的高度；
优点：简单，无需增加新的标签；
缺点：不能和position配合使用，因为超出的尺寸的会被隐藏；

# 使用伪元素:after清除浮动

```
.parent {
    padding: 10px;
    background: red;
}

.parent:after{
    // 定义元素前后的生成内容，这里是定义元素后的空内容
    content: '';    
    display: block;
    clear: both;
}

.child {
    float: left;
    margin-right: 10px;
    width: 50px;
    height: 50px;
    background: green;
}
```

原理：原理同方法一有点类似，在元素最后定义一个空的内容，然后让该空的内容来清除浮动；
优点：无需额外的标签，浏览器兼容性好，是目前用的最多的一种清除浮动的方法之一；
缺点：代码稍微复杂点，初学者可能不太能理解其原理；

<footer>
<hr/>
![footer](footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>






