---
title: 【JS】常见的前端跨域方案集锦
date: 2017-11-01 22:23:41
tags: ['js','跨域']
categories: 'js'
---
![cross-domain](index.jpeg)
浏览器在请求不同域的资源时，会因为同源策略的影响请求不成功，这就是通常被提到的“跨域问题”。作为前端开发，解决跨域问题应该是一个被熟练掌握的技能。而随着技术不断的更迭，针对跨域问题的解决也衍生出了多种解决方案。我们通常会根据项目的不同需要，而采取不同的方式。这篇文章，将详细总结跨域问题的相关知识点，以便在遇到相同问题的时候，能有一个清晰的解决思路。

<!--more-->
## 跨域问题的产生
跨域是由浏览器同源策略限制的一类请求场景。
同源策略（SOP）是一种约定，由Netscape公司1995年引入浏览器，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，浏览器很容易受到XSS、CSFR等攻击。所谓同源是指"协议+域名+端口"三者相同，即便两个不同的域名指向同一个ip地址，也非同源。

同源策略限制以下几种行为：
1. Cookie、LocalStorage 和 IndexDB 无法读取
2. DOM 和 JS对象无法获得
3. AJAX 请求不能发送

## 常见的跨域场景
URL|说明|是否允许通信
--|--|--
http://www.a.com/a.html  http://www.a.com/b.html|同一域名下|允许
http://www.a.com/lab/a.html http://www.a.com/script/b.html|同一域名下不同文件夹|允许
http://www.a.com:8000/a.html http://www.a.com/b.html|同一域名，不同端口|不允许
http://www.a.com/a.html https://www.a.com/b.html|同一域名，不同协议|不允许
http://www.a.com/a.html http://70.32.92.74/b.html|域名和域名对应ip|不允许
http://www.a.com/a.html http://script.a.com/b.html|主域相同，子域不同|不允许
http://www.cnblogs.com/a.html http://www.a.com/b.html|不同域名|不允许

## 解决跨域的方法

 1. 通过jsonp跨域
 2. document.domain + iframe跨域
 3. location.hash + iframe
 4. window.name + iframe跨域
 5. postMessage跨域
 6. 跨域资源共享（CORS）
 7. nginx代理跨域
 8. nodejs中间件代理跨域
 9. WebSocket协议跨域

这里挑选几个简单常见的前端跨域方案来介绍
### 通过jsonp跨域
原理：动态创建`script`标签，利用`script`的`src` 不受同源策略约束来跨域获取数据。
优点：对于浏览器兼容性良好；
缺点：只能用于GET请求，并且需要后端配合对接口做一定的改变。
**原生js实现jsonp**
```javascript
//回调函数
function showData(data) {
    for (let i in data) {
        console.log(i + ':' + data[i]);
    }
}

function getInfo() {
    const script = document.createElement('script');
    const url = 'http://xxxx/xx.php?callback=showData';

    script.type = 'text/javascript';
    script.setAttribute('src', url);

    document.head.appendChild(script);
}

//需要时调用
getInfo();
```
当执行`getInfo()`时，就发送`http://xxxx/xx.php?callback=showData`请求，正常请求该接口时，服务端会直接返回数据，如`{msg:'hello'}`；而当用jsonp时，服务端会接受这个callback参数，然后用这个参数值包装要返回的数据，如`showData({msg:'hello'})`，并且该函数会被立即执行。
**jQuery实现jsonp**
```javascript
$.ajax({
    url: 'http://xxx/xx?callback=?', //不指定回调名，可省略callback参数，会由jQuery自动生成
    dataType: 'jsonp',
    jsonpCallback: 'demo', //用于添加自己的回调函数,无此项则回调函数默认为success
    success: function(data) {
        console.log(data.msg);
    }
});
```
利用`$.ajax`方法，只要指定`dataType`为`jsonp`即可。

### document.domain + iframe跨域
主要用于主域相同，子域不同的页面之间进行交互。
前提条件：这两个域名必须属于同一个基础域名！而且所用的协议，端口都要一致，否则无法利用`document.domain`进行跨域。
原理：两个页面都通过js强制设置`document.domain`为基础主域，就实现了同域。
如：父页面（http://www.blog.com/a.html）
```javascript
<iframe id="iframe" src="http://about.blog.com/b.html"></iframe>
<script>
    document.domain = 'blog.com';
    var user = 'index';
</script>
```
子页面（http://about.blog.com/b.html）
```javascript
<script>
    document.domain = 'blog.com';
    // 获取父窗口中变量
    alert('get js data from parent ---> ' + window.parent.user);
</script>
```
父页面利用`iframe`引入`about.blog.com`的一个`b.html`页面。
如果两个页面不设置`document.domain`，则a页面中可以看到b页面的内容，却不能通过js来操作它。因为这两个页面主域相同，子域不同，在操作之前，js会检测两个页面的域是否相等，如果相等，就允许其操作，如果不相等，就会拒绝操作。
我们通过设置`document.domain`，将两个页面的domain设置成一致，这样就可以在两个页面之间互相操作了。
### location.hash + iframe
原理： a欲与b跨域相互通信，通过中间页c来实现。 三个页面，不同域之间利用`iframe`的`location.hash`传值，相同域之间直接`js`访问来通信。
优点：
1. 可以解决域名完全不同的跨域；
2. 可以实现双向通讯。
缺点：
1. `location.hash`会直接暴露在URL里，并且在一些浏览器里会产生历史记录，数据安全性不高也影响用户体验；
2. 由于URL大小的限制，支持传递的数据量不大；
3. 需新增一个代理文件来实现数据传递。

举个栗子：
页面：
1. first.com/first.html
2. first.com/cross.html
3. second.com/second.html
实现：first.html与second.html的交互
思路：first.html-->second.html-->cross.html-->first.html
1. first.html通过iframe引入second.html；
2. second.html通过iframe引入cross.html；
3. cross.html与first.html同域，可直接通过`js`访问来通信。

first.html(http://www.first.com/first.html)

```javascript
<iframe id="iframe" src="http://www.second.com/second.html" style="display: none;"></iframe>
<input type="button" onClick="changeColor()" value="change color" id="btn"/>

<script>
    var iframe = document.getElementById('iframe');

    function onCallback(hash) {
        if (hash === '#red') {
            document.getElementById('btn').style.color = 'red';
        }
    }

    function changeColor() {
        iframe.src = iframe.src + '#red';
    }
</script>
```

second.html(http://www.second.com/second.html)
```javascript
<iframe id="iframe" src="http://www.first.com/cross.html" style="display: none;"></iframe>

<script>
    var iframe = document.getElementById('iframe');

    window.onhashchange = function () {
        const hash = location.hash;
        iframe.src = iframe.src + hash;
    };
</script>
```
cross.html(http://www.first.com/cross.html)
```javascript
<script>
    window.onhashchange = function () {
        window.parent.parent.onCallback(location.hash);
    };
</script>
```
在first.html中，我们定义一个回调函数`onCallback()`，该函作用是将按钮的颜色改成红色，当我们点击按钮时，执行`changeColor()`事件，将`hash`值传给second.html；
在second.html中，绑定`onhashchange`事件，当`hash`值改变时，因为second.html和first.html不同域，此时直接执行first.html中的`onCallback()`事件，会提示跨域操作的错误信息，所以我们将该`hash`值传给cross.html；
在cross.html中，绑定`onhashchange`事件，当`hash`值改变时，因为cross.html和first.html同域，所以可以执行first.html中的`onCallback()`事件。
这样就实现了first.html和second.html之间的通信。

### window.name + iframe跨域
原理：`window`对象有个`name`属性，该属性有个特征，即在一个窗口(window)的生命周期内，窗口载入的所有的页面都是共享一个`window.name`的，每个页面对`window.name`都有读写的权限，`window.name`是持久存在一个窗口载入过的所有页面中的，并不会因新页面的载入而进行重置，并且可以支持非常长的`name`值（2MB）。

比如有个http://www.a.com/a.html页面，需要通过a.html页面里的`js`来获取另一个位于不同域上的页面www.b.com/b.html里的数据。

a.html(http://www.a.com/a.html)
```javascript
<iframe id="iframe" src="www.b.com/b.html" style="display: none;" onload="getData()"></iframe>

<script>
    function getData() {
        var iframe = document.getElementById('iframe');

        iframe.onload = function () {
            //此时iframe和a.html处于同一域中，可以互相访问
            var data = iframe.contentWindow.name;
            console.log(data);
        };

        //about:blank，javascript: 和 data: 中的内容，继承了载入他们的页面的源，此处也可以设置成www.a.com下的任意页面。
        iframe.src = 'about:blank';
    }
</script>
```
b.html(http://www.b.com/b.html)
```javascript
<script>
    window.name = '{name:"eric",age:27,sex:"man"}';
</script>
```
a.html通过`iframe`引入b.html，当`iframe`加载完成时执行`getData()`函数，因为a.html和b.html是不同域，所以a.html不能直接获取`iframe`中的`name`值，而`getData()`函数的作用就是将`iframe`和a.html设置成同一域，使得a.html能够访问到`iframe`中的数据。
上述代码只是演示`window.name`如何用于跨域操作，实际应用中我们可以对该过程进行封装，比如动态创建`iframe`等，为了安全，获取完数据后，还需销毁该iframe，此处不再示范。

### postMessage跨域
在 HTML5 中， window 对象增加了一个非常有用的方法：
```
windowObj.postMessage(message,targetOrigin)
```
- `windowObj`：接受消息的Window对象。
- `message`：在最新的浏览器中可以是对象。
- `targetOrigin`：目标的源，*表示任意。

可以使用它来向其它的window对象发送消息，无论这个window对象是属于同源或不同源，目前IE8+、FireFox、Chrome、Opera等浏览器都已经支持`window.postMessage`方法。

比如有两个页面：www.a.com/a.html和www.b.com/b.html，用`window.postMessage`来实现两个页面之间相互通信。
a.html(www.a.com/a.html)
```javascript
<iframe id="iframe" src="www.b.com/b.html" style="display: none;"></iframe>

<script>
    var iframe = document.getElementById('iframe');
    iframe.onload = function () {
        var data = {
            name: 'eric',
            age: 27,
            sex: 'man'
        };

        // 向b.html传送跨域数据
        iframe.contentWindow.postMessage(JSON.stringify(data), 'http://www.b.com');
    };

    // 接受b.html返回数据
    window.addEventListener('message', function (e) {
        alert('data from b.html ---> ' + e.data);
    });
</script>
```
b.html(www.b.com/b.html)
```javascript
<script>
    // 接收a.html的数据
    window.addEventListener('message', function (e) {
        alert('data from a.html ---> ' + e.data);

        var data = JSON.parse(e.data);
        if (data) {
            data.number = 16;

            // 处理后再发回a.html
            window.parent.postMessage(JSON.stringify(data), 'http://www.a.com');
        }
    });
</script>
```
a.html页面加载完成时，向b.html传送跨域数据，b.html接收到该数据后，进行了一定处理后又返回给a.html。
其中`message`事件就是用来接收`postMessage`发送过来的请求的。函数参数的属性有以下几个：

- `origin`：发送消息的window的源；
- `data`：数据；
- `source`：发送消息的Window对象。

### 跨域资源共享（CORS）
`CORS`是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）。
它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。
`CORS`需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。
详细解析可以参考阮一峰的`跨域资源共享 CORS 详解`（http://www.ruanyifeng.com/blog/2016/04/cors.html），
主要设置在服务端，这里简单举个例子来演示：
前端设置：
```javascript
var xhr = new XMLHttpRequest(); // IE8/9需用window.XDomainRequest兼容

// 前端设置是否带cookie
xhr.withCredentials = true;

xhr.open('post', 'http://www.b.com:8080/login', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('user=admin');

xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
        alert(xhr.responseText);
    }
};
```
服务端设置：
Java后台：
```java
/*
 * 导入包：import javax.servlet.http.HttpServletResponse;
 * 接口参数中定义：HttpServletResponse response
 */

response.setHeader("Access-Control-Allow-Origin", "http://www.a.com"); // 若有端口需写全（协议+域名+端口）
response.setHeader("Access-Control-Allow-Credentials", "true");
```
PHP后台：
```php
// 设置允许其他域名访问
header('Access-Control-Allow-Origin:*');
// 设置允许的响应类型
header('Access-Control-Allow-Methods:POST, GET');
// 设置允许的响应头
header('Access-Control-Allow-Headers:x-requested-with,content-type');
```
NodeJs后台：
```javascript
var http = require('http');
var server = http.createServer();
var qs = require('querystring');

server.on('request', function (req, res) {
    var postData = '';

    // 数据块接收中
    req.addListener('data', function (chunk) {
        postData += chunk;
    });

    // 数据接收完毕
    req.addListener('end', function () {
        postData = qs.parse(postData);

        // 跨域后台设置
        res.writeHead(200, {
            'Access-Control-Allow-Credentials': 'true',     // 后端允许发送Cookie
            'Access-Control-Allow-Origin': 'http://www.a.com',    // 允许访问的域（协议+域名+端口）
            'Set-Cookie': 'l=a123456;Path=/;Domain=www.b.com;HttpOnly'   // HttpOnly:脚本无法读取cookie
        });

        res.write(JSON.stringify(postData));
        res.end();
    });
});

server.listen('8080');
console.log('Server is running at port 8080...');
```
这里推荐参考张鑫旭写的一个案例`CORS ajax跨域请求php简单完整案例一则`（http://www.zhangxinxu.com/wordpress/2018/02/cors-ajax-xmlhttprequest-php/）来辅助学习。



<footer>
<hr/>
![footer][https://raw.githubusercontent.com/imdwpeng/photoGallery/master/footer.gif]
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>