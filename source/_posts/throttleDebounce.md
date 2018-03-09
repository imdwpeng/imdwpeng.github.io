---
title: 【JS】JavaScript节流和防抖
date: 2017-12-09 20:53:40
tags: "js"
categories: "js"
---
![title](index.jpeg)
针对一些会频繁触发的事件如`scroll`、`resize`，如果正常绑定事件处理函数的话，有可能在很短的时间内多次连续触发事件，十分影响性能。
因此针对这类事件要进行防抖动或者节流处理。

节流：允许一个函数在规定的时间内只执行一次；
防抖：如果一定时间内没有触发执行条件才执行一次；
<!--more-->

## 节流
多应用于页面元素滚动事件，当页面滚动时，一定时间段内只执行一次。
实现思路：

 1. 声明一个变量当标志位，记录当前代码是否在执行；
 2. 如果空闲，则可以正常触发方法执行；
 3. 如果代码正在执行，则取消这次方法执行。

```javascript
// css
#test {
    border: 1px solid black;
    width: 200px;
    height: 100px;
    overflow: scroll;
}

.content {
    height: 1000px;
    background: #dd1144;
}

// html
<div id="test">
    <div class="content"></div>
</div>

// js
let ifScroll = true;
document.getElementById('test').addEventListener('scroll', () => {
    if (!ifScroll) return;

    ifScroll = false;
    setTimeout(() => {
        console.log('函数节流');
        ifScroll = true;
    }, 1000);
});
```
上述代码中，我们绑定`scroll`事件，页面滚动的时候每隔1秒执行定时器内部操作。

## 防抖
多应用于检验输入框的验证信息，如账号输入完毕后才会判断是否输入正确的格式。
实现思路：执行事件时先清除上一次的定时器，再重新开始该定时器，当一段时间后没有再执行该事件，则执行定时器的内容。

```javascript
// html
<input id="name" type="text">

// js
let timer = false;
document.getElementById('name').addEventListener('keydown', () => {
    clearTimeout(timer);

    timer = setTimeout(() => {
        alert('函数防抖：输入完毕，可以开始验证了！');
    }, 1000);
});
```
上述代码中，我们绑定输入框的`keydown`事件，只有在停止输入1秒后才会去验证输入框的内容。
<footer>
<hr/>
![footer](http://dwpblog.site/images/footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>