---
title: JavaScript设计模式
date: 2017-06-16 21:45:23
tags: ['js','设计模式']
categories: 'js'
---
每个JS开发者都力求写出可维护、复用性和可读性高的代码。随着应用不断扩大，代码组织的合理性也越来越重要。设计模式为特定环境下的常见问题提供了一个组织结构，对于克服这些挑战起到至关重要的作用。
<!--more-->
## 单体（Singleton）模式
**——用来划分命名空间并将一批属性和方法组织在一起的对象，如果它可以被实例化，那么它只能被实例化一次**
```javascript
// 单体模式
var Singleton = function(name){
    this.name = name;
    this.instance = null;
};
Singleton.prototype.getName = function(){
    return this.name;
}

// 获取实例对象
function getInstance(name) {
    //只实例化一次
    if(!this.instance) {
        this.instance = new Singleton(name);
    }
    return this.instance;
}

// 测试单体模式的实例
var a = getInstance("aa");
var b = getInstance("bb");

console.log(a === b);   // true
console.log(a.getName());   // aa
console.log(b.getName());   // aa
```

**优点**
* 可以用来划分命名空间，减少全局变量的数量。
* 使用单体模式可以使代码组织的更为一致，使代码容易阅读和维护。
* 可以被实例化，且实例化一次。

-----------

## 工厂（Factory）模式
**——提供一个创建一系列相关或相互依赖对象的接口，而无需指定他们具体的类**
工厂就是把成员对象的创建工作转交给一个外部对象，好处在于消除对象之间的耦合(何为耦合？就是相互影响)。通过使用`工厂方法`而不是new关键字及具体类，**可以把所有实例化的代码都集中在一个位置**，有助于创建模块化的代码，这才是工厂模式的目的和优势。
工厂模式分为简单工厂模式和抽象工厂模式。
### 简单工厂模式
另外使用一个类（通常是一个单体）来生成实例
```javascript
var m = {};
m.action = function (){
    console.log('dog');
}

//外部对象创建成员对象
var demo = function (){
    m.action();
}

//调用
demo();   
```
这种模式把成员对象的创建工作转交给一个外部对象。这个外部对象可以像本例中一样是一个简单的命名空间，也可以是一个类的实例。
**优点**：能解决多个相似的问题。
**缺点**：不能知道对象识别的问题(对象的类型不知道)。
如以下例子：
```javascript
function CreatePerson(name,age,sex) {
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.sex = sex;
    obj.sayName = function(){
        return this.name;
    }
    return obj;
}
var p1 = new CreatePerson("longen",'28','男');
var p2 = new CreatePerson("tugenhua",'27','女');
console.log(p1.name); // longen
console.log(p1.age);  // 28
console.log(p1.sex);  // 男
console.log(p1.sayName()); // longen

console.log(p2.name);  // tugenhua
console.log(p2.age);   // 27
console.log(p2.sex);   // 女
console.log(p2.sayName()); // tugenhua

// 返回都是object 无法识别对象的类型 不知道他们是哪个对象的实列
console.log(typeof p1);  // object
console.log(typeof p2);  // object
console.log(p1 instanceof Object); // true
```
### 抽象工厂模式
先设计一个抽象类，这个类不能被实例化，只能用来派生子类，最后通过对子类的扩展实现工厂方法。
父类只对创建过程中的一般性问题进行处理，这些处理会被子类继承，子类之间是相互独立的，具体的业务逻辑会放在子类中进行编写。
父类就变成了一个抽象类，但是父类可以执行子类中相同类似的方法，具体的业务逻辑需要放在子类中去实现。
```javascript
var f = function() {};
f.prototype = {
    c: function() {
        throw new Error('can\'t use this method');//如果调用此方法会报错，因为它是用来派生子类不能实例化
    }
};
var e = function() {
    f.call(this);
}
e.prototype = new f();
e.prototype.constructor = e;
e.prototype.c = function() {
    console.log('this method is redefine');
}
// 调用
var demo = new e();
demo.c();
```
下面来看一个例子，比如我现在开几个自行车店，那么每个店都有几种型号的自行车出售。我们现在来使用工厂模式来编写这些代码;
父类的构造函数如下：
```javascript
// 定义自行车的构造函数
var BicycleShop = function(){};
BicycleShop.prototype = {
    constructor: BicycleShop,
    /*
    * 买自行车这个方法
    * @param {model} 自行车型号
    */
    sellBicycle: function(model){
        var bicycle = this.createBicycle(mode);
        // 执行A业务逻辑
        bicycle.A();

        // 执行B业务逻辑
        bicycle.B();

        return bicycle;
    },
    createBicycle: function(model){
        throw new Error("父类是抽象类不能直接调用，需要子类重写该方法");
    }
};
```
上面是定义一个自行车抽象类来编写工厂模式的实列，定义了createBicycle这个方法，但是如果直接实例化父类，调用父类中的这个createBicycle方法,会抛出一个error，因为父类是一个抽象类，他不能被实列化，只能通过子类来实现这个方法，实现自己的业务逻辑，下面我们来定义子类，我们学会如何使用工厂模式重新编写这个方法，首先我们需要继承父类中的成员，然后编写子类；对上述代码进行改写如下：
```javascript
// 定义自行车的构造函数
var BicycleShop = function(name){
    this.name = name;
    this.method = function(){
        return this.name;
    }
};
BicycleShop.prototype = {
    constructor: BicycleShop,
    /*
    * 买自行车这个方法
    * @param {model} 自行车型号
    */
    sellBicycle: function(model){
            var bicycle = this.createBicycle(model);
            // 执行A业务逻辑
            bicycle.A();

            // 执行B业务逻辑
            bicycle.B();

            return bicycle;
        },
        createBicycle: function(model){
            throw new Error("父类是抽象类不能直接调用，需要子类重写该方法");
        }
    };
    // 实现原型继承
    function extend(Sub,Sup) {
        //Sub表示子类，Sup表示超类
        // 首先定义一个空函数
        var F = function(){};

        // 设置空函数的原型为超类的原型
        F.prototype = Sup.prototype; 

        // 实例化空函数，并把超类原型引用传递给子类
        Sub.prototype = new F();
                
        // 重置子类原型的构造器为子类自身
        Sub.prototype.constructor = Sub;
                
        // 在子类中保存超类的原型,避免子类与超类耦合
        Sub.sup = Sup.prototype;

        if(Sup.prototype.constructor === Object.prototype.constructor) {
            // 检测超类原型的构造器是否为原型自身
            Sup.prototype.constructor = Sup;
        }
    }
    var BicycleChild = function(name){
        this.name = name;
        // 继承构造函数父类中的属性和方法
        BicycleShop.call(this,name);
    };
    // 子类继承父类原型方法
    extend(BicycleChild,BicycleShop);
// BicycleChild 子类重写父类的方法
BicycleChild.prototype.createBicycle = function(){
    var A = function(){
        console.log("执行A业务操作");    
    };
    var B = function(){
        console.log("执行B业务操作");
    };
    return {
        A: A,
        B: B
    }
}
var childClass = new BicycleChild("龙恩");
console.log(childClass.name);  // 龙恩
childClass.sellBicycle("mode"); // 打印出  执行A业务操作和执行B业务操作
```
**优点**
* 弱化对象间的耦合，防止代码的重复。在一个方法中进行类的实例化，可以消除重复性的代码。
* 重复性的代码可以放在父类去编写，子类继承于父类的所有成员属性和方法，子类只专注于实现自己的业务逻辑。

---------------

## 桥接（bridge）模式
*——将抽象部分与它的实现部分分离，使它们都可以独立地变化。*
桥接模式最常见和实际的应用场合之一是事件监听器回调函数。
先看一段代码：
```javascript
addEvent(element, 'click', getBeerById);
function getBeerById(e) {
	var id = this.id;
	asyncRequest('GET', 'beer.uri?id=' + id, function(resp) {
	// Callback response.
	console.log('Requested Beer: ' + resp.responseText);
	});
}
```
上述代码，有个问题就是getBeerById必须要有浏览器的上下文才能使用，因为其内部使用了this.id这个属性，如果没用上下文，那就歇菜了。所以说一般稍微有经验的程序员都会将程序改造成如下形式：
```javascript
function getBeerById(id, callback) {
	// 通过ID发送请求，然后返回数据
	asyncRequest('GET', 'beer.uri?id=' + id, function(resp) {
		// callback response
		callback(resp.responseText);
	});
}

addEvent(element, 'click', getBeerByIdBridge);
　　function getBeerByIdBridge (e) {
　　　　getBeerById(this.id, function(beer) {
　　　　　　console.log('Requested Beer: '+beer);
　　});
}
```
这里的getBeerByIdBridge就是我们定义的桥，有了这层桥接元素，这个API的使用范围大大拓宽了，这给了你更大的设计自由。将抽象的click事件和getBeerById连接起来，只需要提供一个ID和回调函数，就可以在单元测试中运行这个API了，也可以在命令行环境中对这个接口进行快速测试。

------------

## 组合（Composite）模式
*——将对象组合成树形结构以表示“部分-整体”的层次结构。它使得客户对单个对象和复合对象的使用具有一致性*
### 适用场景
1. 存在一批组织成某种层次体系的对象。
2. 希望对这批对象或者其中某一部分对象实施一个操作。 
将一组对象组合成树形结构，并统一对待组合对象和叶对象，忽略它们之间的不同（因为叶对象也可以也可以包含叶对象而成为组合对象），组合模式中的对象只能是一对多的关系，不能出现多对一。
一个组合对象包含多个叶对象。每个基本单元又可以是别的组合对象的叶对象类似文件夹与它里面的内容的关系网，一个文件夹或文件又可以是其它文件夹的内容，但一个文件夹或文件不能同时属于多个上级文件夹。
在JavaScript中实现组合模式时，要保证组合对象和叶对象拥有相同的接口方法，对同一组叶对象的操作必须具有一致性。
定义组合对象:
```javascript
var Folder = function (name) {
    this.name = name;
    this.parent = null;
    this.files = [];
    this.element = document.createElement('div');
    this.element.className = 'folder';
    this.element.innerHTML = name;
    this.element.style.paddingLeft = '20px';

};
Folder.prototype = {
    constructor: Folder,

    add: function (file) {
        file.parent = this;
        if (this.files.indexOf(file) === -1) {
            this.files.push(file);
            console.log('\'' + file.name + '\'添加成功');

            this.element.appendChild(file.getElement());
        } else {
            console.log('\'' + file.name + '\'已存在,添加失败');
        }
    },

    scan: function () {
        if (this.parent) {
            console.log('开始扫描\'' + this.parent.name + '\': ' + this.name);
        } else {
            console.log('开始扫描根目录: ' + this.name);
        }
        //关键在这里，调用所有它的叶对象的接口方法scan()
        for (var i = 0, file; file = this.files[i++];) {
            file.scan();
        }
    },

    remove: function (file) {

        var n = this.files.indexOf(file);

        if (n === -1) {
            console.log('无法删除: \'' + file.name + '\'不存在:');
        }
        if (n >= 0) {
            this.files.splice(n, 1);
            this.element.removeChild(file.getElement());
            console.log('成功删除:' + file.name);
        }
    },

    getElement: function () {
        this.element.innerHTML = this.name;
        return this.element;
    }

};
```
定义叶对象：
```javascript
var File = function (name) {
    this.name = name;
    this.parent = null;
    this.element = document.createElement('div');
    this.element.className = 'file';
    this.element.style.paddingLeft = '20px';

};
File.prototype = {
    constructor: Folder,

    add: function () {
        console.log('不能添加在文件下面');
    },

    scan: function () {
        console.log(this.parent.name + ': ' + this.name);
    },

    remove: function (file) {
        console.log('无法删除: \'' + file.name + '\'不存在:');
    },

    getElement: function () {
        this.element.innerHTML = this.name;
        return this.element;
    }
};
```
测试：
```javascript
var folder = new Folder('目录');
var folder1 = new Folder('一');
var folder2 = new Folder('二');
var folder3 = new Folder('2.3');

var file11 = new File('1.1');
var file12 = new File('1.2');
var file21 = new File('2.1');
var file22 = new File('2.2');
var file311 = new File('3.1.1');

folder.add(folder1);  //'一'添加成功
folder.add(folder2);  //'二'添加成功
folder.add(folder2);  //'二'已存在,添加失败
folder1.add(file11);  //'1.1'添加成功
folder1.add(file12);  //'1.2'添加成功
folder2.add(file21);  //'2.1'添加成功
folder2.add(file22);  //'2.2'添加成功
folder2.add(folder3);  //'2.3'添加成功
folder3.add(file311);  //'3.1.1'添加成功
folder2.remove(file311);  //无法删除: '3.1.1'不存在:
folder2.remove(file22);  //成功删除:2.2
file11.add(file12);  //不能添加在文件下面
folder.scan();  //开始扫描根目录: 目录
                //开始扫描'目录': 一
                //一: 1.1
                //一: 1.2
                //开始扫描'目录': 二
                //二: 2.1
                //开始扫描'二': 2.3
                //2.3: 3.1.1
document.body.appendChild(folder.element);
```

----------

## 模块（Module）模式
模块是任何强大应用程序架构中不可或缺的一部分，它通常能够帮助我们清晰地分离和组织项目中的代码单元。
模块模式的思路是为单体模式添加私有变量和私有方法能够减少全局变量的使用；如下就是一个模块模式的代码结构：
```javascript
var singleMode = (function(){
    // 创建私有变量
    var privateNum = 112;
    // 创建私有函数
    function privateFunc(){
        // 实现自己的业务逻辑代码
    }
    // 返回一个对象包含公有方法和属性
    return {
        publicMethod1: publicMethod1,
        publicMethod2: publicMethod1
    };
})();
```
模块模式使用了一个返回对象的匿名函数。在这个匿名函数内部，先定义了私有变量和函数，供内部函数使用，然后将一个对象字面量作为函数的值返回，返回的对象字面量中只包含可以公开的属性和方法。这样的话，可以提供外部使用该方法；由于该返回对象中的公有方法是在匿名函数内部定义的，因此它可以访问内部的私有变量和函数。
### 适用场景
如果我们必须创建一个对象并以某些数据进行初始化，同时还要公开一些能够访问这些私有数据的方法，那么我们这个时候就可以使用模块模式了。
### 放大模式
如果一个模块很大，必须分成几个部分，或者一个模块需要继承另一个模块，这时就有必要采用`放大模式`（augmentation）。
```javascript
var module1 = (function (mod){

　　　　mod.m3 = function () {
　　　　　　//...
　　　　};

　　　　return mod;

　　})(module1);
```
上面的代码为module1模块添加了一个新方法m3()，然后返回新的module1模块。
### 宽放大模式
在浏览器环境中，模块的各个部分通常都是从网上获取的，有时无法知道哪个部分会先加载。如果采用上一节的写法，第一个执行的部分有可能加载一个不存在空对象，这时就要采用`宽放大模式`。
```javascript
　var module1 = ( function (mod){

　　　　//...

　　　　return mod;

　　})(window.module1 || {});
```
与`放大模式`相比，`宽放大模式`就是"立即执行函数"的参数可以是空对象。
### 输入全局变量
独立性是模块的重要特点，模块内部最好不与程序的其他部分直接交互。
为了在模块内部调用全局变量，必须显式地将其他变量输入模块。
```javascript
　var module1 = (function ($, YAHOO) {

　　　　//...

　　})(jQuery, YAHOO);
```
上面的module1模块需要使用jQuery库和YUI库，就把这两个库（其实是两个模块）当作参数输入module1。这样做除了保证模块的独立性，还使得模块之间的依赖关系变得明显。

----------

## 观察者(Observer)模式
**--当一个对象的状态发生变化时，能够自动通知其他关联对象，自动刷新对象状态**
这是一种创建*松散耦合*代码的技术，在这种模式中，有两类对象，分别为`观察者（Observer）`和`目标对象（Subject）`。
目标对象中保存着一份观察者的列表，当目标对象的状态发生改变时就主动向观察者发出通知（调用观察者提供的方法），从而建立一种发布/订阅的关系。
**优点**
* 支持简单的广播通信，自动通知所有已经订阅过的对象。
* 页面载入后目标对象很容易与观察者存在一种动态关联，增加了灵活性。
* 目标对象与观察者之间的抽象耦合关系能够单独扩展以及重用。
**使用场合**
当一个对象的改变需要同时改变其它对象，并且它不知道具体有多少对象需要改变的时候，就应该考虑使用观察者模式。
```javascript
var Event = {
    // 存放函数的数组 list
    lists: [ ],
    // 通过on接口添加监听的回调函数进入lists数组
    on: function (eventName, callback) {
        if (!this.lists[eventName]) {
            this.lists[eventName] = [ ];
        }
        this.lists[eventName].push(callback);
    },
    // 传递参数触发回调函数列表
    emit: function (eventName) {
        if (this.lists[arguments[0]]) {
            for (var i = 0; i < this.lists[arguments[0]].length; i++) {
                this.lists[arguments[0]][i](arguments[1]);
            }
        }
    }
};

//测试
//绑定事件
Event.on('test', function (result) {
    console.log(result);
});
Event.on('test', function () {
    console.log('test');
});
//触发事件
Event.emit('test', 'hello');    //输出 hello   test
```
总的来说，观察者模式所做的工作就是在解耦，让耦合的双方都依赖于抽象，而不是依赖于具体。从而使得各自的变化都不会影响到另一边的变化。

<footer>
<hr/>
![footer][https://raw.githubusercontent.com/imdwpeng/photoGallery/master/footer.gif]
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>










	



