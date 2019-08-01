---
title: 【前端测试】基于Jest+Enzyme+React的前端测试
date: 2019-04-02 13:38:45
tags: ['jest','enzyme','前端测试']
categories: '前端测试'
---
![jest](index.jpeg)
本文中的自动化测试指的是单元测试 （UT），所谓单元测试也就是对每个单元进行测试，通俗的将一般针对的是函数，类或单个组件，不涉及系统和集成。单元测试是软件测试的基础测试，主要是用来验证所测代码是否和程序员的期望一致。
<!--more-->

## 背景

### 什么是测试
一段检测你的应用代码（也叫“生产代码”）是否按预期执行的代码。


### 为什么要测试
如果你不测试，有些潜在的bug可能会在未来的时间里一次又一次的困扰你，从部署到生产都将会是一个噩梦。

- 正确性：测试可以验证代码的正确性，在上线前做到心里有底；
- 自动化：当然手工也可以测试，通过console可以打印出内部信息，但是这是一次性的事情，下次测试还需要从头来过，效率不能得到保证。通过编写测试用例，可以做到一次编写，多次运行；
- 解释性：测试用例用于测试接口、模块的重要性，那么在测试用例中就会涉及如何使用这些API。其他开发人员如果要使用这些API，那阅读测试用例是一种很好地途径，有时比文档说明更清晰；
- 驱动开发，指导设计：代码被测试的前提是代码本身的可测试性，那么要保证代码的可测试性，就需要在开发中注意API的设计，TDD将测试前移就是起到这么一个作用；
- 保证重构：互联网行业产品迭代速度很快，迭代后必然存在代码重构的过程，那怎么才能保证重构后代码的质量呢？有测试用例做后盾，就可以大胆的进行重构；

### 测试框架

![grameworks](1.png)

**Jest**

- 基于 Jasmine 至今已经做了大量修改添加了很多特性
- 开箱即用配置少，API简单
- 包含单元测试运行器、断言库、Mock库
- 互动模式选择要测试的模块
- 基于 `Istanbul` 的内置代码覆盖率报告
- 全局环境，比如 describe 不需要引入直接用
- 较多用于 React 项目(但广泛支持各种项目)

**Mocha**

- 灵活(不包括断言和仿真，自己选对应工具)
- 流行的选择：chai，sinon
- 社区成熟用的人多，测试各种东西社区都有示例
- 需要较多配置
- 可以使用快照测试，但依然需要额外配置

**Jasmine**

- 开箱即用(支持断言和仿真)
- 全局环境
- 比较'老',坑基本都有人踩过了

## Jest

### 测试方法

方法|说明
---|---
beforeAll|在文件开始执行运行的函数
afterAll|在文件开始执行后运行的函数
beforeEach|在每个测试 `test` 执行前运行的函数
afterEach|在每个测试 `test` 执行后运行的函数
describe|测试组块，包含多个测试
test|测试用例，别名 `it`
expect|执行一个断言

### 常见断言
匹配器（Matchers）是 `Jest` 中非常重要的一个概念，它可以提供很多种方式来让你去验证你所测试的返回值，这里文重点介绍几种常用的 `Matcher` ，其他的可以通过 [官网api文档](https://jestjs.io/docs/en/expect) 查看。

匹配器|说明
---|---
toBe()|等于（不能用于对象）
toEqual()|等于（可用于对象）
not.toBe()|不等于
toBeNull()|只匹配 `null`
toBeUndefined()|只匹配 `undefined`
toBeDefined()|与 `toBeUndefined` 相反
toBeTruthy()|匹配任何 `if` 语句为真
toBeFalsy()|匹配任何 `if` 语句为假
toBeGreaterThan()|匹配大于
toBeGreaterThanOrEqual()|匹配大于等于
toBeLessThan()|匹配小于
toBeLessThanOrEqual()|匹配小于等于
toBeCloseTo()|匹配浮点数相等
toMatch()|匹配字符串，支持正则匹配
toContain()|匹配数据包含特定子项

## 用法

### 项目初始化
```
npm init -y
```

### 安装依赖包

安装 `jest`
```
npm i jest -D
```

如用到es6语法，则还要安装 `Babel` 依赖
```
npm i babel-jest @babel/core @babel/preset-env -D
```

安装版本
```
"devDependencies": {
    "@babel/core": "^7.5.5",
    "@babel/preset-env": "^7.5.5",
    "babel-jest": "^24.8.0",
    "jest": "^24.8.0"
}
```

**注：Jest 24 放弃了对 Babel 6 的支持。如果不能升级到 Babel 7 ，那么要么继续使用 Jest 23 ，要么升级到 Jest 24 ，将 `babel-jest` 锁定在版本23;**

### 配置

修改 `package.json`
```
{
  "scripts": {
    "test": "jest --colors --coverage" // coverage 测试覆盖率报告  colors 根据覆盖率生成不同颜色
  }
}
```

根目录新建 `.babelrc`
```
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "targets": {
                    "node": "current"
                }
            }
        ]
    ]
}
```

### 测试用例

根目录下新建文件
```
// add.js
const add = (a,b) => {
    return a + b;
}

export default add;
```

```
// add.test.js
import add from './add';

describe('Test Add',()=>{
    it('add 1 and 2 should equal 3',()=>{
        expect(add(1,2)).toEqual(3);
    });
});
```

终端执行命令 `npm run test`
![result_1](result_1.png)

## Enzyme
[enzyme](https://airbnb.io/enzyme) 是 `Airbnb` 开源的 `react` 测试类库，提供了一套简洁强大的 API，着重于渲染 `react` 组件和 `DOM` 节点处理，开发体验十分友好；

### 渲染组件方法

方法|说明
---|---
shallow|浅层渲染，渲染至虚拟DOM，之渲染当前组件，只能对当前组件进行断言
mount|渲染真实DOM节点，渲染当前节点及其所有子节点，用于DOM API的交互或测试组件的生命周期等
render|渲染html，用于分析html结构

### 常见api

api|说明
---|---
find|通过匹配选择器来检索节点
at(index)|返回指定位置的子组件
get(index)|返回指定位置的DOM节点
first|返回第一个子组件
last|返回最后一个子组件
props|返回组件的所有属性
prop(key)|返回组件指定属性
state(key)|返回组件指定状态
setProps(nextProps)|设置组件属性
setState(nextState)|设置组件状态

## React + Jest + Enzyme

首先配置好 `React16`+ `Webpack4` 环境，提供一个参考环境 [Webpack+React配置](https://github.com/imdwpeng/webpack-config) ，这里不再展开介绍；

### 安装和配置

安装 `Enzyme` 
需要根据使用的 `React` 版本来安装 `enzyme-adapter-react`，这里使用的是 React 16版，对应 `enzyme-adapter-react-16`
```
npm i enzyme enzyme-adapter-react-16 -D
```

配置文件

```
// .babelrc
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "targets": {
                    "node": "current"
                }
            }
        ],
        "@babel/preset-react"
    ],
    "plugins": [
        "@babel/plugin-proposal-class-properties",
        "@babel/plugin-syntax-dynamic-import"
    ]
}
```

```
// jest.setup.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';
// 注册enzyme
configure({ adapter: new Adapter() });
```

`jest` 配置，更多配置见[官网文档](https://jestjs.io/docs/en/configuration)
```
// jest.config.js
module.exports = {
  setupFiles: ['<rootDir>/jest.setup.js'], // 运行测试前可执行的脚本
  transform: {
    '^.+\\.(js|jsx|mjs)$': '<rootDir>/node_modules/babel-jest'
  },
  testMatch: [  // 测试文件的路径
    '<rootDir>/src/**/__tests__/**/*.{js,jsx,mjs}'
  ],
  transformIgnorePatterns: ['<rootDir>/node_modules/']
};
```

### 测试用例

新建一个 `Input` 组件
```
// src/Input.js
import React, { Component } from 'react';

class Input extends Component {
  constructor() {
    super();
    this.state = {
      value: ''
    };
  }

  handleChange = (e) => {
    this.setState({
      value: e.target.value
    });
  }

  render() {
    const { value } = this.state;

    return (
      <input
        type="text"
        value={value}
        onChange={this.handleChange}
      />
    );
  }
}

export default Input;
```

新建对应测试用例
```
// src/__test__/Input.test.js
import React from 'react';
import { shallow } from 'enzyme';
import Input from '../Input';

describe('Input', () => {
  it('render component', () => {
    const wrapper = shallow(<Input />);
    expect(wrapper.type()).toEqual('input');
  });

  it('value change', () => {
    const wrapper = shallow(<Input />);
    expect(wrapper.state('value')).toEqual('');
    wrapper.simulate('change', { target: { value: 'lisi' } });
    expect(wrapper.state('value')).toEqual('lisi');
  });
});
```
这里定义了2个测试内容

- 测试组件渲染
- 测试触发事件方法 `onChange`

终端执行命令 `npm run test`
![result_2](result_2.png)



<footer>
<hr/>
![footer](footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>