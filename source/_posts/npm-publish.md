---
title: 在NPM上发布自己的React组件
date: 2018-01-19 21:16:43
tags: ["npm","js","react"]
categories: "npm"
---
![title](index.jpeg)
&emsp;&emsp;接触`React`半年多了，期间用过`antd`、`react-bootstrap`等`React`组件库，但是有些不能满足我们的千奇百怪的需求，所以自己开始依葫芦画瓢地造轮子。时间久了，项目中就多了各种各样特有的零散组件，看起看很杂，而且重用性不好。

&emsp;&emsp;你要相信前端都是些不安分的人，既然发现问题了，于是又开始琢磨能不能自己弄个NPM组件包呢，这样又好维护，又能增大重用性，说干就干，各种百度、bing搞起来。

<!--more-->


## 目录结构
首先，我们约定一下目录结构：

 - **src**： 下用于存放源代码；
 - **lib**： 是编译后的代码，这个目录只读；
 - **test**： 测试代码目录

这是截图我的目录结构，供大家参考：
(此处应有图片catalogue)
 
## 创建模块
```
mkdir react-npm
cd react-npm 
npm init
```
然后就一路回车，最后在项目根目录下创建了一个package.json。
![package](package-1.png)
此时创建模块成功。

这里简单介绍一下package.json各属性说明：

 - **name** - 包名；
 - **version** - 包的版本号；
 - **description** - 包的描述；
 - **homepage** - 包的官网 url；
 - **author** - 包的作者姓名；
 - **contributors** - 包的其他贡献者姓名；
 - **dependencies** - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下；
 - **repository** - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上；
 - **main** - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js；
 - **keywords** - 关键字；

## 编写组件
新建src目录，在此目录下编写我们的组件。
这里大家自由发挥，我就举个例子，供大家参考。
Button组件：src/Button/Button.js
```javascript
import React, {Component} from 'react';

class Button extends Component {
    render() {
        return (
            <button type="button">此处有按钮</button>
        )
    }
}

export default Button;
```

Table组件：src/Table/Table.js
```javascript
import React, {Component} from 'react';

class Table extends Component {
    render() {
        return (
            <table>
                <thead>
                <tr>
                    <th>title1</th>
                    <th>title2</th>
                    <th>title3</th>
                    <th>title4</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                    <td>content</td>
                    <td>content</td>
                    <td>content</td>
                    <td>content</td>
                </tr>
                <tr>
                    <td>content</td>
                    <td>content</td>
                    <td>content</td>
                    <td>content</td>
                </tr>
                <tr>
                    <td>content</td>
                    <td>content</td>
                    <td>content</td>
                    <td>content</td>
                </tr>
                </tbody>
            </table>
        )
    }
}

export default Table;
```

入口文件：src/index.js
```javascript
import Table from './Table/Table';
import Button from './Button/Button';

export {Table,Button};
```

## Babel转码
我们不能直接在浏览器中运行ES6语法，所以我们需要用`Babel`来实现ES6到ES5的代码转换。
执行一下命令进行安装：
```
npm install --save-dev babel-cli babel-core babel-loader babel-plugin-istanbul babel-preset-es2015 babel-preset-react babel-preset-stage-0 react react-dom
```

 - babel-preset-es2015：ES2015转码规则
 - babel-preset-react：react转码规则
 - babel-preset-stage-0：ES7不同阶段语法提案的转码规则（共有4个阶段）

安装完成后会自动更新`package.json`：
![package](package-2.png)

修改`package.json`，修改我们的入口文件，并且添加以下转码的指令：

```javascript
"main": "./lib/index.js",
"scripts": {
    "clean": "rm -rf lib && mkdir lib",
    "lib": "npm run clean && babel src --out-dir lib --copy-files"
}
```

`Babel`相关参数:

 - **--out-dir lib**：指定输出目录为 lib；
 - **--extensions .es6,.es,.jsx**：指定需要编译的文件类型；
 - **--copy-files**：对于不需要编译的文件直接拷贝；
 - **--source-maps**：生成 souce-map 文件；


此时的`package.json`的配置如下
![package](package-3.png)
 
然后，在项目根目录下新建`.babelrc`配置文件
```javascript
{
  "presets": [
    "es2015",
    "stage-0",
    "react"
  ],
  "plugins": [
    "transform-react-jsx"
  ]
}
```
运行指令：
```
npm run lib
```
该指令作用：先清空`lib`文件目录，然后再将`src`目录下的文件转码成ES5输出到`lib`目录下。
![run-lib](run-lib.png)

至此，转码已经基本完成。

## 创建测试文件

程序猿都是很严谨、负责任的，所以在完成组件的编写之后，我们要在本地测试一下，至少要保证我们分享的组件库可用吧。
这里使用`create-react-app`脚手架创建一个`test`项目，并`link`我们刚才完成的组件。
项目根目录下执行如下指令：
```
npm run lib  // 需先进行转码
npm link     // Mac系统需加sudo，然后输入本机密码
create-react-app test
cd test
npm link react-npm  // react-npm是我们的组件名称，即package.json中的name值
```
然后我们就可以在test项目中直接引用本地'react-npm'组件。
修改test/src/App.js
```javascript
import React, {Component} from 'react';
import {Button, Table} from 'react-npm';
import './App.css';

class App extends Component {
    render() {
        return (
            <div>
                <Button/>
                <Table/>
            </div>
        );
    }
}

export default App;
```
在test项目下运行指令：
```
npm start
```
然后，我们就可以在浏览器中看到以下效果了。
![liulanqi](liulanqi.png)

## 发布至NPM
代码测试没问题后，我们就可以开始着手发布到NPM。
### 注册npm账号
注册账号有两种方法：

 - 直接去[npm官网](https://www.npmjs.com/)，自己注册个账号；
 - 执行`npm adduser`指令，按照提示创建；

```
$ npm adduser
Username: your name
Password: your password
Email: (this IS public) your email
```
 首次登录，需`npm login`存储证书到本地，后面就不需要每次都登录了。
 ```
 $ npm login
Username: your name
Password: your password
Email: your email
 ```
 我们可以执行`npm whoami`来检验是否成功登录。
 
### 指定发布文件
如果我们需要只发布某些文件到npm上，则可以在`package.json`中设置`files`即可
![package-4](package-4.png)
 
### 开始发布
执行以下指令开始发布
```
npm publish
```
当输出以下结果时，那么恭喜你，你的组件包已发布成功了
![success](success-1.png)
不过，如果你点背的话会出现各种错误，这里列举几个常见错误：

 - **镜像错误**

![error-2](error-2.png)
这是因为网络问题，许多小伙伴把npm的镜像代理到淘宝或者别的地方了，这里要设置回原来的镜像。
```
npm config set registry=http://registry.npmjs.org
```

 - **组件包名称被占**

![error-1](error-1.png)
这是因为你的组件包名称在npm上已经被占用啦，这时候你就去需要去npm搜索你的模块名称，如果搜索不到，就可以用，并且把`package.json`里的`name`修改过来，重新`npm publish`。
如我刚开始的组件名`react-npm`，该名称已被注册了，所以我后来就改成了`react-npm-tt`。

### 版本更新
当我们修改bug，或者添加新功能时，需要更新版本，然后才能发布
```
npm version 0.1.1
npm publish
```
或者直接在`package.json`里修改`version`。

### 版本管理
一般来说版本分成三部分：A.B.C

 - 主版本号（A）：当你做了不兼容的 API 修改，0表示处于开发阶段；
 - 次版本号（B）：当你做了向下兼容的功能性新增;
 - 修订号（C）：当你做了向下兼容的问题修正。

### 自动转码

现在的情况是每次我们发布前都要手动执行`npm run lib`来将ES6转码成ES5，但是每次都要发布前都要手动去编译一次，要知道，我们程序猿是能坐着就不站着的，这根本不符合我们程序猿的懒性，so，就有了` prepublish script`功能。
修改`package.json`：
![package-5](package-5.png)
这样，我们每次执行`npm publish`时，会首先自动执行`npm run lib`去编码，对嘛，这不就省了我们再去输入一行指令了嘛，程序猿就该如此，能省则省。

## 持续集成
![小徽章](chixu.png)
目前npm上开源的项目实在是太多，从中找出靠谱的项目要花费一定的精力跟时间去验证，所以开发者都会对自己的开源项目持续更新，并且经过测试的项目更加值得信赖。
持续集成(Continuous integration)的目的，就是让产品可以快速迭代，同时还能保持高质量。它的核心措施是，代码集成到主干之前，必须通过自动化测试。只要有一个测试用例失败，就不能集成。
有关持续集成的内容将在下一篇文章展开，这里不再过多介绍，尽请期待。

## 结语
至此，整个NPM发布流程顺利结束，本文不涉及代码发布至Git仓库，因为我觉得有发布自己的npm包这种想法的小伙伴一般都不是新手吧，相信对Git已经了解的不能再了解了，不过，如果有新手还不太了解Git，可以看看我之前写的一篇文章[Git 学习手扎](http://dwpblog.site/2017/09/16/git/)。







<footer>
<hr/>
![footer](http://dwpblog.site/images/footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>