---
title: React学习
date: 2017-06-18 13:06:54
tags: 'react'
categories: 'react'
---
<img class="react-img" src="./react/logo.png"/>
React 是一个用于构建用户界面的 JAVASCRIPT 库。
React主要用于构建UI，很多人认为 React 是 MVC 中的 V（视图）。
React 起源于 Facebook 的内部项目，用来架设 Instagram 的网站，并于 2013 年 5 月开源。
React 拥有较高的性能，代码逻辑非常简单，越来越多的人已开始关注和使用它。
<!--more-->
## React 安装
React.js 单独使用基本上是不可能的事情。不要指望着类似于 jQuery 下载放到 <head /> 标签就开始使用。使用 React.js 不管在开发阶段生产阶段都需要一堆工具和库辅助，编译阶段你需要借助 `Babel`；需要 `Redux` 等第三方的状态管理工具来组织代码；如果你要写单页面应用那么你需要 `React-router`。这就是所谓的`React.js全家桶`。
我们这里会直接使用 React.js 官网所推荐使用的工具 `create-react-app` 工具。它可以帮助我们一键生成所需要的工程目录，并帮我们做好各种配置和依赖，也帮我们隐藏了这些配置的细节。也就是所谓的“开箱即用”。
工具地址：[https://github.com/facebookincubator/create-react-app](https://github.com/facebookincubator/create-react-app)
首先安装[node.js](http://nodejs.cn/download/),下载系统对应的版本进行安装即可。
然后安装`create-react-app`工具。
```javascript
npm install -g create-react-app    //installation
create-react-app my-app            //creating an App
cd my-app/
npm start
```

----------

## React概述
React 的核心思想是：`封装组件`。
各个组件维护自己的状态和 UI，当状态变更，自动重新渲染整个组件。
基于这种方式的一个直观感受就是我们不再需要不厌其烦地来回查找某个 DOM 元素，然后操作 DOM 去更改 UI。
React 大体包含下面这些概念：
* 组件
* JSX
* Virtual DOM
* Data Flow

这里通过一个简单的组件来快速了解这些概念，以及建立起对 React 的一个总体认识。
```javascript
import React, { Component } from 'react';
import { render } from 'react-dom';

class HelloMessage extends Component {
  render() {
    return <div>Hello {this.props.name}</div>;
  }
}

// 加载组件到 DOM 元素 mountNode
render(<HelloMessage name="John" />, mountNode);
```
### 组件
React 应用都是构建在组件之上。
上面的 `HelloMessage` 就是一个 React 构建的组件，最后一句 `render` 会把这个组件显示到页面上的某个元素 `mountNode` 里面，显示的内容就是 `<div>Hello John</div>`。
`props` 是组件包含的两个核心概念之一，另一个是 `state`（这个组件没用到）。可以把 `props` 看作是组件的配置属性，在组件内部是不变的，只是在调用这个组件的时候传入不同的属性（比如这里的 `name`）来定制显示这个组件。
### JSX
从上面的代码可以看到将 HTML 直接嵌入了 JS 代码里面，这个就是 React 提出的一种叫 JSX 的语法，这应该是最开始接触 React 最不能接受的设定之一，因为前端被“表现和逻辑层分离”这种思想“洗脑”太久了。但实际上组件的 HTML 是组成一个组件不可分割的一部分，能够将 HTML 封装起来才是组件的完全体，React 发明了 JSX 让 JS 支持嵌入 HTML 不得不说是一种非常聪明的做法，让前端实现真正意义上的组件化成为了可能。
好消息是你可以不一定使用这种语法，后面会进一步介绍 JSX，到时候你可能就会喜欢上了。现在要知道的是，要使用包含 JSX 的组件，是需要“编译”输出 JS 代码才能使用的。
### Virtual DOM
当组件状态 `state` 有更改的时候，React 会自动调用组件的 `render` 方法重新渲染整个组件的 UI。
当然如果真的这样大面积的操作 DOM，性能会是一个很大的问题，所以 React 实现了一个Virtual DOM，组件 DOM 结构就是映射到这个 Virtual DOM 上，React 在这个 Virtual DOM 上实现了一个 diff 算法，当要重新渲染组件的时候，会通过 diff 寻找到要变更的 DOM 节点，再把这个修改更新到浏览器实际的 DOM 节点上，所以实际上不是真的渲染整个 DOM 树。这个 Virtual DOM 是一个纯粹的 JS 数据结构，所以性能会比原生 DOM 快很多。
Data Flow
“单向数据绑定”是 React 推崇的一种应用架构的方式。当应用足够复杂时才能体会到它的好处，虽然在一般应用场景下你可能不会意识到它的存在，也不会影响你开始使用 React，你只要先知道有这么个概念。

----------

## React JSX
React 使用 JSX 来替代常规的 JavaScript。
JSX 是一个看起来很像 XML 的 JavaScript 语法扩展。
我们不需要一定使用 JSX，但它有以下优点：
* JSX 执行更快，因为它在编译为 JavaScript 代码后进行了优化。
* 它是类型安全的，在编译过程中就能发现错误。
* 使用 JSX 编写模板更加简单快速。

### 使用 JSX
利用 JSX 编写 DOM 结构，可以用原生的 HTML 标签，也可以直接像普通标签一样引用 React 组件。这两者约定通过大小写来区分，小写的**字符串**是 HTML 标签，大写开头的**变量**是 React 组件。
**使用 HTML 标签：**
```javascript
import React from 'react';
import { render } from 'react-dom';

var myDivElement = <div className="foo" />;  //HTML标签
render(myDivElement, document.getElementById('mountNode'));
```
HTML 里的 `class` 在 JSX 里要写成 `className`，因为 `class` 在 JS 里是保留关键字。同理某些属性比如 `for` 要写成 `htmlFor`。
**使用组件：**
```javascript
import React from 'react';
import { render } from 'react-dom';
import MyComponent from './MyComponet';

var myElement = <MyComponent someProperty={true} />;  //组件
render(myElement, document.body);
```
**使用 JavaScript 表达式**
属性值使用表达式，只要用 `{}` 替换 `""`:
```javascript
// Input (JSX):
var person = <Person name={window.isLoggedIn ? window.name : ''} />;
// Output (JS):
var person = React.createElement(
  Person,
  {name: window.isLoggedIn ? window.name : ''}
);
```
子组件也可以作为表达式使用：
```javascript
// Input (JSX):
var content = <Container>{window.isLoggedIn ? <Nav /> : <Login />}</Container>;
// Output (JS):
var content = React.createElement(
  Container,
  null,
  window.isLoggedIn ? React.createElement(Nav) : React.createElement(Login)
);
```
**注释**
在 JSX 里使用注释也很简单，就是沿用 JavaScript，唯一要注意的是在一个组件的子元素位置使用注释要用 `{}` 包起来。
```javascript
var content = (
  <Nav>
      {/* child comment, put {} around */}
      <Person
        /* multi
           line
           comment */
        name={window.isLoggedIn ? window.name : ''} // end of line comment
      />
  </Nav>
);
```
**HTML 转义**
React 会将所有要显示到 DOM 的字符串转义，防止 XSS。所以如果 JSX 中含有转义后的实体字符比如 `&copy;` (©) 最后显示到 DOM 中不会正确显示，因为 React 自动把 `&copy;` 中的特殊字符转义了。有几种解决办法：
* 直接使用 UTF-8 字符 ©
* 使用对应字符的 Unicode 编码
* 使用数组组装 `<div>{['cc ', <span>&copy;</span>, ' 2015']}`</div>
* 直接插入原始的 HTML

```javascript
<div dangerouslySetInnerHTML={{__html: 'cc &copy; 2015'}} />
```
### 属性扩散
有时候你需要给组件设置多个属性，你不想一个个写下这些属性，或者有时候你甚至不知道这些属性的名称，这时候 spread attributes 的功能就很有用了。

比如：
```javascript
var props = {};
props.foo = x;
props.bar = y;
var component = <Component {...props} />;
```
`props` 对象的属性会被设置成 `Component` 的属性。
属性也可以被覆盖：
```javascript
var props = { foo: 'default' };
var component = <Component {...props} foo={'override'} />;
console.log(component.props.foo); // 'override'
```
写在后面的属性值会覆盖前面的属性。

---------

## React 组件
可以这么说，一个 React 应用就是构建在 React 组件之上的。
组件有两个核心概念：
* props
* state
一个组件就是通过这两个属性的值在 `render` 方法里面生成这个组件对应的 HTML 结构。
注意：组件生成的 HTML 结构只能有一个单一的根节点。
**props**
前面也提到很多次了，`props` 就是组件的属性，由外部通过 JSX 属性传入设置，一旦初始设置完成，就可以认为 `this.props` 是不可更改的，所以**不要**轻易更改设置 this.props 里面的值（虽然对于一个 JS 对象你可以做任何事）。
**state**
state 是组件的当前状态，可以把组件简单看成一个“状态机”，根据状态 `state` 呈现不同的 UI 展示。
一旦状态（数据）更改，组件就会自动调用 `render` 重新渲染 UI，这个更改的动作会通过 `this.setState` 方法来触发。
**划分状态数据**
一条原则：让组件尽可能地少状态。
这样组件逻辑就越容易维护。
什么样的数据属性可以当作状态？
当更改这个状态（数据）需要更新组件 UI 的就可以认为是 `state`，下面这些可以认为**不是**状态：
* 可计算的数据：比如一个数组的长度
* 和 props 重复的数据：除非这个数据是要做变更的
最后回过头来反复看几遍 [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)，相信会对组件有更深刻的认识。
**无状态组件**
你也可以用纯粹的函数来定义无状态的组件(stateless function)，这种组件没有状态，没有生命周期，只是简单的接受 props 渲染生成 DOM 结构。无状态组件非常简单，开销很低，如果可能的话尽量使用无状态组件。比如使用箭头函数定义：
```javascript
const HelloMessage = (props) => <div> Hello {props.name}</div>;
render(<HelloMessage name="John" />, mountNode);
```
因为无状态组件只是函数，所以它没有实例返回，这点在想用 refs 获取无状态组件的时候要注意，<a href="#DOM">参见DOM 操作</a>。
### 组件生命周期
一般来说，一个组件类由 `extends Component` 创建，并且提供一个 `render` 方法以及其他可选的生命周期函数、组件相关的事件或方法来定义。
**初始化状态**
如果是使用 ES6 的语法，可以在构造函数中初始化状态，比如：
```javascript
class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = { count: props.initialCount };
  }

  render() {
    // ...
  }
}
```
**设置 props 的默认数据**
如果是使用 ES6 语法，可以直接定义 `defaultProps` 这个类属性，这样能更直观的知道 default props 是预先定义好的对象值：
```javascript
Counter.defaultProps = { initialCount: 0 }
```
**render**
**必须**
组装生成这个组件的 HTML 结构（使用原生 HTML 标签或者子组件），也可以返回 `null` 或者 `false`，这时候 `ReactDOM.findDOMNode(this)` 会返回 `null`。
**生命周期函数**
组件的生命周期可分成三个状态：
* **挂载**: 组件被插入到DOM中。
* **更新**: 组件被重新渲染，查明DOM是否应该刷新。
* **移除**: 组件从DOM中移除。

React提供`生命周期`方法，可以在这些方法中放入自己的代码。有will方法，会在某些行为发生之前调用，和did方法，会在行为发生之后调用。

生命周期的方法有：
**挂载**
* `getInitialState()` 在组件被挂载之前调用。状态化的组件应该实现这个方法，返回初始的state数据。该方法在ES6中已被弃用，state需在`constructor`中设置。
* `componentWillMount()` 在挂载发生之前立即被调用。
* `componentDidMount()` 在挂载结束之后立即被调用。需要DOM节点的初始化操作应该放在这里。之后组件已经生成了对应的DOM结构，可以通过`this.getDOMNode()`来进行访问。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用`setTimeout`, `setInterval`或者发送`AJAX`请求等操作(防止异部操作阻塞UI)。

**更新**
* `componentWillReceiveProps(object nextProps)` 当一个挂载的组件接收到新的props时被调用。该方法用于比较this.props和nextProps，然后使用`this.setState()`来改变state。这个方法在初始化`render`时**不会**被调用。

```javascript
componentWillReceiveProps: function(nextProps) {  
  this.setState({
    likesIncreasing: nextProps.likeCount > this.props.likeCount
  });
}
```

* `shouldComponentUpdate(object nextProps, objct nextState):boolean` 当组件做出是否要更新DOM的决定时被调用，实现该函数，优化`this.props`和`nextProps`，以及`this.state`和nextState的比较，如果不需要React更新DOM，则返回false。  
* `componentWillUpdate(object nextProps,object nextState)` 在更新发生之前被调用。可在这里调用`this.setState()`。
* `componentDidUpdate(object prevProps,object prevState)` 在更新发生之后被调用。

**移除**
* `componentWillUnmount()` 在组件移除和销毁之前被调用。清理工作应放在这里。

以下实例初始化 state ， setNewCount 用于更新 state。所有生命周期在 Counter 组件中。
```javascript
import React,{Component} from 'react';
import ReactDOM from 'react-dom';

class Counter extends Component {
    constructor(props) {
        super(props);
        this.state = {count: props.count}
    }

    componentWillMount() {
        console.log('Component WILL MOUNT!')
    }

    componentDidMount() {
        console.log('Component DID MOUNT!')
    }

    componentWillReceiveProps(newProps) {
        console.log('Component WILL RECEIVE PROPS!')
    }

    shouldComponentUpdate(newProps, newState) {
        return true;
    }

    componentWillUpdate(nextProps, nextState) {
        console.log('Component WILL UPDATE!')
    }

    componentDidUpdate(prevProps, prevState) {
        console.log('Component DID UPDATE!')
    }

    componentWillUnmount() {
        console.log('Component WILL UNMOUNT!')
    }

    setNewCount() {
        this.setState({count: this.state.count + 1})
    }

    render(){
        return(
            <div>
                <button onClick={this.setNewCount.bind(this)}>更新</button>
                <p>{this.state.count}</p>
            </div>
        )
    }
}
Counter.defaultProps = {count:1}

ReactDOM.render(<Counter />, document.getElementById('root'));
```
页面刚加载时输出
```
Component WILL MOUNT!
Component DID MOUNT!
```
点击更新后输出
```
Component WILL UPDATE!
Component DID UPDATE!
```
### 事件处理
React 里面绑定事件的方式和在 HTML 中绑定事件类似，使用驼峰式命名指定要绑定的 `onClick` 属性为组件定义的一个方法 `{this.handleClick.bind(this)}`。
注意要显式调用 `bind(this)` 将事件函数上下文绑定要组件实例上，这也是 React 推崇的原则：没有黑科技，尽量使用显式的容易理解的 JavaScript 代码。
**传统事件处理**
传统事件处理中，人们使用 `element.addEventListener` 和**事件委托**设置事件监听的原因。委托意味着你可以在某个父节点监听事件。
比如当一个 <div> 包含多个按钮时，只需为所有的按钮设置一个监听器即可。
```javascript
<div id="parent">
    <button type="button" id="ok">OK</button>
    <button type="button" id="cancel">Cancel</button>
</div>

<script>
    document.getElementById('parent').addEventListener('click',function(event) {
      var button = event.target;
      //根据具体点击的按钮进行不同操作
      switch(button.id){
          case 'ok':
              console.log('OK!');
              break;
          case 'cancel':
              console.log('Cancel!');
              break;
          default:
              new Error('Unexpected button ID');
      };
    });
</script>
```
传统事件处理虽然方法奏效，性能也不错，但仍然有些缺点：
* 监听器的声明代码远离视图组件，使得代码难以搜索和调试。
* 使用委托总要经过switch结构，在你开始进行实际逻辑编写前，需要创建不必要的样板代码。
* 实际中为处理浏览器的不一致（上述代码中省略了这一步骤），会让代码变得更加臃肿。

**React事件处理**
为了包裹并规范浏览器事件，React使用了**合成事件**来消除浏览器之间的不一致情况。
合成事件会以事件委托（event delegation）的方式绑定到组件最上层，并且在组件卸载（unmount）的时候自动销毁绑定的事件。
React在事件处理中使用驼峰法命名，因此你需要用 `onClick` 代替 onclick。
```javascript
class LikeButton extends Component {
  constructor(props) {
    super(props);
    this.state = { liked: false };
  }

  handleClick(e) {
    this.setState({ liked: !this.state.liked });
  }

  render() {
    const text = this.state.liked ? 'like' : 'haven\'t liked';
    return (
      <p onClick={this.handleClick.bind(this)}>
          You {text} this. Click to toggle.
      </p>
    );
  }
}
```
### <label id="DOM">DOM操作</label>
大部分情况下你不需要通过查询 DOM 元素去更新组件的 UI，你只要关注设置组件的状态 `setState`。但是可能在某些情况下你确实需要直接操作 DOM。
首先我们要了解 `ReactDOM.render` 组件返回的是什么？
它会返回对组件的引用也就是组件实例（对于无状态状态组件来说返回 null），注意 JSX 返回的不是组件实例，它只是一个 `ReactElement` 对象，比如这种：
```javascript
// A ReactElement
const myComponent = <MyComponent />

// render
const myComponentInstance = ReactDOM.render(myComponent, mountNode);
myComponentInstance.doSomething();
```
**findDOMNode()**
当组件加载到页面上之后（mounted），你都可以通过 `react-dom` 提供的 `findDOMNode()` 方法拿到组件对应的 DOM 元素。
```javascript
import { findDOMNode } from 'react-dom';

// Inside Component class
componentDidMound() {
  const el = findDOMNode(this);
}
```
`findDOMNode()` 不能用在无状态组件上。
**Refs**
另外一种方式就是通过在要引用的 DOM 元素上面设置一个 `ref` 属性指定一个名称，然后通过 `this.refs.name` 来访问对应的 DOM 元素。
比如有一种情况是必须直接操作 DOM 来实现的，你希望一个 `<input/>` 元素在你清空它的值时 focus，你没法仅仅靠 `state` 来实现这个功能。
```javascript
class App extends Component{
    constructor(){
        super();
        this.state = {
            userInput:''
        }
    }
    
    handleChange(e){
        this.setState({userInput:e.target.value});
    }
    
    clearAndFocus(){
        this.setState({userInput:''},()=>{
            this.refs.userInput.focus();
        });
    }
    
    render(){
        return(
            <div>
                <button type="button" onClick={this.clearAndFocus.bind(this)}>Click to Focus and Reset</button>
                <input 
                    type="text" 
                    ref="userInput" 
                    value={this.state.userInput} 
                    onChange={this.handleChange.bind(this)}
                />
            </div>
        );
    }
}
```
如果 `ref` 是设置在原生 HTML 元素上，它拿到的就是 DOM 元素，如果设置在自定义组件上，它拿到的就是组件实例，这时候就需要通过 `findDOMNode` 来拿到组件的 DOM 元素。
因为无状态组件没有实例，所以 ref 不能设置在无状态组件上，一般来说这没什么问题，因为无状态组件没有实例方法，不需要 ref 去拿实例调用相关的方法，但是如果想要拿无状态组件的 DOM 元素的时候，就需要用一个状态组件封装一层，然后通过 `ref` 和 `findDOMNode` 去获取。
**总结**
* 你可以使用 ref 到的组件定义的任何公共方法，比如 `this.refs.myTypeahead.reset()`
* Refs 是访问到组件内部 DOM 节点唯一**可靠**的方法
* Refs 会自动销毁对子组件的引用（当子组件删除时）

**注意事项**
* 不要在 `render` 或者 `render` 之前访问 `refs`
* 不要滥用 `refs`，比如只是用它来按照传统的方式操作界面 UI：找到 DOM -> 更新 DOM
### 组合组件
使用组件的目的就是通过构建模块化的组件，相互组合组件最后组装成一个复杂的应用。
在 React 组件中要包含其他组件作为子组件，只需要把组件当作一个 DOM 元素引入就可以了。
一个例子：一个显示用户头像的组件 Avatar 包含两个子组件 ProfilePic 显示用户头像和 ProfileLink 显示用户链接：
```javascript
import React from 'react';
import { render } from 'react-dom';

const ProfilePic = (props) => {
  return (
    <img src={'http://graph.facebook.com/' + props.username + '/picture'} />
  );
}

const ProfileLink = (props) => {
  return (
    <a href={'http://www.facebook.com/' + props.username}>
      {props.username}
    </a>
  );
}

const Avatar = (props) => {
  return (
    <div>
      <ProfilePic username={props.username} />
      <ProfileLink username={props.username} />
    </div>
  );
}

render(
  <Avatar username="pwh" />,
  document.getElementById('example')
);
```
通过 `props` 传递值。
**循环插入子元素**
如果组件中包含通过循环插入的子元素，为了保证重新渲染 UI 的时候能够正确显示这些子元素，每个元素都需要通过一个特殊的 `key` 属性指定一个唯一值。
`key` 必须直接在循环中设置：
```javascript
const ListItemWrapper = (props) => <li>{props.data.text}</li>;

const MyComponent = (props) => {
  return (
    <ul>
      {props.results.map((result) => {
        return <ListItemWrapper key={result.id} data={result}/>;
      })}
    </ul>
  );
}
```
`this.props.children`
组件标签里面包含的子元素会通过 `props.children` 传递进来。
比如：
```javascript
React.render(<Parent><Child /></Parent>, document.body);
React.render(<Parent><span>hello</span>{'world'}</Parent>, document.body);
```
HTML 元素会作为 React 组件对象、JS 表达式结果是一个文字节点，都会存入 `Parent` 组件的 `props.children`。
一般来说，可以直接将这个属性作为父组件的子元素 render：
```javascript
const Parent = (props) => <div>{props.children}</div>;
```
`props.children` 通常是一个组件对象的数组，但是当只有一个子元素的时候，`props.children` 将是这个唯一的子元素，而不是数组了。
`React.Children` 提供了额外的方法方便操作这个属性。
### 组件间通信
**父子组件间通信**
这种情况下很简单，就是通过 `props` 属性传递，在父组件给子组件设置 `props`，然后子组件就可以通过 `props` 访问到父组件的数据／方法，这样就搭建起了父子组件间通信的桥梁。
```javascript
import React, {Component} from 'react';
import ReactDOM from 'react-dom';

class GroceryList extends Component {
    constructor() {
        super();
        this.state = {
            num: ''
        }
    }

    handleClick(i) {
        this.setState({num: this.props.items[i]});
    }

    render() {
        return (
            <div>
                {this.props.items.map((item, i) => {
                    return (
                        <button type="button" onClick={this.handleClick.bind(this, i)} key={i}>{item}</button>
                    )
                })}
                <p>You have clicked: {this.state.num}</p>
            </div>
        );
    }
}

ReactDOM.render(<GroceryList items={['one', 'two', 'three']}/>, document.getElementById('root'));
```
`div` 可以看作一个子组件，指定它的 `onClick` 事件调用父组件的方法。
父组件访问子组件？用 `refs`
**非父子组件间的通信**
使用全局事件 Pub/Sub 模式，在 `componentDidMount` 里面订阅事件，在 `componentWillUnmount` 里面取消订阅，当收到事件触发的时候调用 `setState` 更新 UI。
这种模式在复杂的系统里面可能会变得难以维护，所以看个人权衡是否将组件封装到大的组件，甚至整个页面或者应用就封装到一个组件。
一般来说，对于比较复杂的应用，推荐使用类似 Flux 这种单项数据流架构，参见<a href="#dataFlow">Data Flow</a>。

## <label id="dataFlow">Data Flow</label>
Data Flow 只是一种应用架构的方式，比如数据如何存放，如何更改数据，如何通知数据更改等等，所以它不是 React 提供的额外的什么新功能，可以看成是使用 React 构建大型应用的一种最佳实践。
正因为它是这样一种概念，所以涌现了许多实现，这里主要关注一种实现--`Redux`
### Redux






