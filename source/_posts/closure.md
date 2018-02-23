---
title: 【JS】菜鸟学闭包
date: 2017-10-05 19:21:54
tags: ['js','闭包']
categories: 'js'
---
![closure](index.jpeg)
相信学习前端的小伙伴们，对这个词应该并不陌生吧，闭包（closure）是JS的一个重点和难点。
本文主要是想和大家一起来简单归纳一下闭包的相关知识。
<!--more-->


## 闭包是什么
何为闭包？这里引用《JavaScript高级程序设计》中的一句话：

> **闭包**是指有权访问另一个函数作用域中的变量的函数。
创建闭包的常见方式，就是在一个函数内部创建另一个函数。

不理解？没关系，可以暂时跳过这段话，等看完全文再返回来理解这句话。

## 闭包的形成
为什么会有闭包？闭包是在怎样的条件下产生的？带着这一疑问我们接下去看。
提到闭包的产生，我们不得不提下JS的变量作用域。
JS的变量作用域有两种：`全部变量`和`局部变量`。
**在JS语言中，在函数内部可以访问`全局变量`，而在函数外部不能访问其他函数的`局部变量`。**什么意思呢？举个形象的例子，就比如，公园是开放的，我们都可以随便去公园里散步，但是每个人的房子是私人的，你会让别人随便去你家的后花园散步吗？答案是否定的，我们来看个例子：
```
var name = 'eric';

function func() {
    console.log(name);
}

func();    // eric
```
定义全局变量`name`，当执行`func`函数时，可以输出全局变量`name`为`eric`，在看下面的这个例子：

```
function func() {
    var name = 'eric';
}

console.log(name);    // error
```
在函数`func`内部定义局部变量`name`，然后全局环境下输出`name`，结果为空，因为我们在`func`函数外是访问不到`func`函数内部定义的局部变量的。
那么如果因为某些需求，我们需要在函数外部访问到其他函数内部的局部变量，这该如何实现呢？
```
function func() {
    var name = 'eric';

    function getName() {
        console.log(name);
    }

    return getName;
}

var todo = func();
todo();    // eric
```
**JS语言特有"链式作用域"结构，子对象会一级一级地向上寻找所有父对象的变量。**
在`func`函数内部，我们又创建了一个函数`getName`，`getName`函数的作用是输出`func`的局部变量`name`，因为`getName`是在`func`内部创建的，所以`func`所有的局部变量对于`getName`来说都是可见的，但反过来则不成立，`getName`中的局部变量，对于`func`是不可见的。
当执行`var todo = func()`时，我们执行`func()`函数并将`getName`函数作为值返回给`todo`，当执行`todo()`时就相当于执行了`getName()`，并取得局部变量`name`，所以最终输出为`eric`。原理很简单，因为`getName()`在`func()`的作用域外被执行，所以该作用域不会被垃圾回收起回收，会一直存在，当调用`todo()`时便会访问到`func()`内部的`name`变量。该例子中，`getName()`就是一个闭包。

## 闭包的使用场景
了解什么事闭包以及闭包的产生原因之后，我们再来看下闭包的使用场景。
### 保护变量
闭包可以将一些不希望暴露在全局的变量封装成“私有变量”，如上一个例子中`name`就被封装成了“私有变量”。

### 维护变量
闭包也可以将局部变量常驻内存，防止被GC回收。
```
function func() {
    var num = 0;

    function add() {
        num++;
        console.log(num);
    }

    return add;
}

var count1 = func();
var count2 = func();

count1();   // 1
count1();   // 2
count2();   // 1
count1();   // 3
```
上述例子中我们实例化了两个函数`count1`和`count2`，当执行`count1()`时，就相当于执行`add()`，对变量`num`进行累加操作，输出1，再次执行`count1()`，再进行累加，输出2；然后又执行`count2()`，此时会对cuont2函数绑定的变量`num`进行累加，所以会输出1，然后当我们再次执行`count1()`时，我们会发现现在输出的是3，而不是2。因为两次调用同一个函数，创建的栈是不同的，因此栈内的局部变量是不同的，也就是说`count1`和`count2`函数各自维护一个变量，这些变量不会被GC回收，除非我们手动去删除该变量。

### 在函数执行前提供参数
如`setTimeout`函数在执行的时候是无法传递参数的，那对于这种情况，我们可以用闭包来实现传参。
```
function func1(num) {
    console.log(num);
}

function func2(num) {
    return function () {
        console.log(num);
    }
}


setTimeout(func1, 1000);       // undefined,传参无效
setTimeout(func1(1), 1000);    // 立刻输出1，不会等1秒之后再输出
setTimeout(func2(2), 1000);    // 等待一秒后，输出2
```

## 闭包经典实例
学习前端，关键在于多练，代码敲多了，自然而然就会了，下面来介绍几个闭包的经典实例，来帮助大家巩固闭包知识。
### 循环记录数组（或者循环绑定事件）
这是最经典的闭包面试题，相信很多人看到这例子应该都会印象深刻吧，前端第一次找工作时，这道题估计被我们刷的滚瓜烂熟了吧。
```
var attr = [];
for (var i = 0; i < 10; i++) {
    attr[i] = function () {
        return i;
    };
}

attr[1]();    //10
```
我们需要的一个1到10的数组，如果按上述代码这样执行，我们会发现最终保存的数组里每个值都为10。这是因为变量i在整个函数内都有效，没有得到释放。所以每一次循环，新的i值都会覆盖旧值，导致最后输出的是最后一轮的i的值。大白话就是你还没执行`attr[1]()`函数，for循环就执行完了，最终i值为10，所以等你执行`attr[1]()`函数时，取到的就是最终的i值，即10。
解决思路：
方法一、采用闭包形式，把变量值存起来
```
var attr = [];
for (var i = 0; i < 10; i++) {
    attr[i] = (function (num) {
        return num;
    })(i);
}

attr[1];    // 1
```
此方法是采用立即执行函数增加诺干个闭包域空间将变量储存起来，建立私有变量`num`，尽管引用的`i`值随外部变化而变化，但私有变量`num`始终不变，这个其实可以理解成`attr[i]=i`。
方法二、
```
var attr = [];
for (var i = 0; i < 10; i++) {
	(function(num){
        attr[i] = function () {
            return num;
        }
    })(i);
}

attr[1]();    //1
```
与方法一类似，也是增加诺干个闭包域空间来储存变量。
不同的是方法一是直接将新增的匿名函数中返回的值赋给数组，也就是说我们最终获得的是一组值的数组，而不是一个函数集合，效果同`attr[i]=i`是相同的；本例是在新增的匿名闭包空间内将函数赋值给变量`attr[i]`，最终获得的是一个函数集合。

### 嵌套函数
```
function fun(x, y) {
    console.log(y);
    return {
        fun: function (z) {
            return fun(z, x);
        }
    };
}

var a = fun(0);    // undefined
a.fun(1);          // 0
a.fun(2);          // 0
a.fun(3);          // 0

var b = fun(0).fun(1).fun(2).fun(3);         // undefined   0   1   2

var c = fun(0).fun(1);       // undefined   0   1
c.fun(2);                    // 1
c.fun(3);                    // 1
```
首先要了解，这道题中`fun`函数和函数返回的`fun`属性不是同一个东西，这个要清楚，不要搞混了。
如果不能理清逻辑，建议你拿支笔在纸上画下，下面跟着我的逻辑来过一遍：
`fun()`函数接受两个变量`x`,`y`，如果只输入一个变量，则第二个变量就是`undefined`。
首先`var a = fun(0)`，执行如下：
```
fun(0,y){    // x=0,y=undefined
    console.log(y);   // undefined,因为未定义y值
    return {
        func: function(z){
            return fun(z,0);
        }
    }
}
```
结果返回一个`fun`属性，指向一个函数对象，该对象带有闭包，可以访问到`fun()`函数的局部变量`x`，`x`此时为0，即：
```
function(z){
    return fun(z,0);
}
```
然后执行`a.fun(1)`，传入z的值为1，即执行fun(1,0);
```
fun(1,0){    // x=1,y=0
    consoel.log(0);    // 0
    ...
}
```
同理，执行`a.fun(2)`、`a.fun(3)`时，分别传入z的值为2，3，即分别执行`fun(2,0)`和`fun(3,0)`，输出结果都为0。
然后`var b = fun(0).fun(1).fun(2).fun(3)`，我们拆开一个一个看，与等同于下面这个：
```
var b0 = fun(0),       // undefined
    b1 = b0.fun(1),    // 0
    b2 = b1.fun(2),
    b3 = b2.fun(3);
```
`b0`和`b1`同之前的`a`和`a.fun(1)`是一样的道理，这里直接跳过，如果还不太清楚可以返回去再看一下a的解析过程。
当执行`b1 = b0.fun(1)`时，又会返回一个`fun`属性：
```
fun(1,0){    
    consoel.log(0);    // 0
    return {
        fun: function(z){
            return fun(z,1);
        }
    }
}
```
同理，该`fun`属性也指向一个函数对象，该对象带有闭包，可以访问到`fun()`函数的局部变量`x`，`x`此时为1，这就跟执行`var b0 = fun(0)`的道理是一样的。
然后执行`b2 = b1.fun(2)`，这个和执行`b1 = b0.fun(1)`又是一样的：
```
fun(2,1){    
    consoel.log(0);    // 1
    return {
        fun: function(z){
            return fun(z,2);
        }
    }
}
```
同理可以推断出执行`b3 = b2.fun(3)`的结果。
执行`c`就是先执行`b`，再执行`a`，道理都是一样的，这里不在累赘。
上述这个例子中就想表达一个意思：**闭包中的变量是被储存在内存中，不会被GC回收**。
### 闭包中的`this`对象

> **this**对象是运行时基于函数的执行环境绑定的：在全局函数中，`this`等于`window`，而当函数被作为某个对象的方法调用时，`this`等于那个对象。    --《Javascript高级程序设计》

```
var name = "The Window";
var object = {
    name: "My object",
    getNameFunc: function() {
        return function() {
            return this.name;
        };
    }
}
alert(object.getNameFunc()());    // "The Window"
```
为什么最后输出的不是My Object，而是The Window呢，让我们来解析下。
首先，把最后一句拆开：
```
var a = object.getNameFunc();
var b = a();
```
第一步，获取`a`为返回的匿名函数，此时，`getNameFunc()`是作为`object`的方法调用，返回一个匿名函数；
第二部，调用`a`函数，即调用该匿名函数，此时我们会发现该匿名函数是在全局环境下被调用的，而不是在`object`对象中被调用，所以`this`指向的是全局环境，即`window`。
那么，如何才能获取外部作用域中的`this`呢？
我们可以将该this单独保存成闭包变量：
```
var name = "The Window";
var object = {
    name: "My object",
    getNameFunc: function() {
        var _this = this;
        return function() {
            return _this.name;
        };
    }
}
alert(object.getNameFunc()());    // "My object"
```
我们将`this`保存为闭包变量`_this`，该变量会一致被保存下来，不会因为函数执行结束被GC收回，所以我们在调用匿名函数时，可以访问到该变量，而匿名函数内部的`this`依旧还是指向`window`。


















<footer>
<hr/>
![footer](footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>