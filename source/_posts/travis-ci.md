---
title: 【Git】持续集成之Travis CI自动化部署
date: 2018-03-12 11:39:15
tags: "github travis-ci 自动化部署"
categories: "github"
---
![title](index.jpeg)

<!--more-->

## 持续集成

持续集成（CI）指的是在一个项目中，只要代码库有变更，都会触发CI服务器自动对项目进行构建，自动运行测试，甚至自动部署到测试环境。

持续集成的好处在于，早发现，早治疗；意思就是每次代码的小幅变更，就能看到运行结果，从而不断累积小的变更，而不是在开发周期结束时，一下子合并一大块代码。

## Travis CI

Travis CI 是一个在线的，分布式的持续集成服务，用来构建及测试在GitHub托管的代码。
目前市面上有很多自动化工具，相对于众多的自动化工具来说，用Travis来进行持续集成，不需要自己搭服务器，在网页上点几下就好，用起来更方便，最重要的是，它对开源项目是免费的。

## 准备工作
首先，你要有个Github账号。
什么？你说你没有Github账号？！那赶紧偷偷自行去注册一个。
啥？你不会Git？！介个，出门左拐，给你个教程([「Git」菜鸟教程
](http://dwpblog.site/2017/09/16/git/))，自己慢慢体会，什么时候练成绝世神功了，我们再战。

其次，你要在Github上有一个可运行的项目，没有的话这里提供一个测试用的项目，大家自行前往Fork。

## 开始表演

### 登录Travis CI

访问Travis CI首页，用Github账号登录
![travis-1](travis-1.png)

### 同步Git项目

点击右上角头像，选择`Profile`，然后在显示的页面中点击 `Sync account`，同步Git上的项目。
![travis-2](travis-2.png)
![travis-3](travis-3.png)

### Github生成访问令牌
这是为了授权给Travis用的
访问Github官网，登录账号，点击右上角头像 > Settings > Developer settings > Personal access tokens > Generate new token > Generate token> Copy Token
![git-1](git-1.png)
![git-2](git-2.png)
![git-3](git-3.png)
![git-4](git-4.png)
访问权限如无特殊要求，则按图片中的来。
![git-5](git-5.png)

### 配置构建信息

找到我们本次需要集成的项目，选中，然后点击设置图标，进行配置相关信息。
![travis-4](travis-4.png)
![travis-5](travis-5.png)

### .travis.yml
在项目的根目录下新建一个`.travis.yml`文件，内容如下：
(yml)
```javascript
language: node_js
# nodejs版本
node_js:
    - '8'

# Travis-CI Caching
cache:
  directories:
    - node_modules

# S: Build Lifecycle
install:
  - npm install

before_script:

# 无其他依赖项所以执行npm test 构建就行了
script:
  - npm test

# 阶段成功时执行
after_success:
  - cd ./test
  - git init
  - git config user.name "${U_NAME}"
  - git config user.email "${U_EMAIL}"
  - git add .
  - git commit -m "Update tools"
  - git push --force --quiet "https://${GIT_TOKEN}@${GH_REF}" master:${P_BRANCH}
# E: Build LifeCycle

# 阶段失败时执行
after_failure:
  - git status

#指定分支，只有指定的分支提交时才会运行脚本
branches:
  only:
    - master
env:
 global:
   # 我将其添加到了travis-ci的环境变量中
   #- GH_REF: github.com/imdwpeng/travis-test.git
```
Travis 为上面这些阶段提供了7个钩子。

 - **before_install**：install 阶段之前执行；
 - **before_script**：script 阶段之前执行；
 - **after_failure**：script 阶段失败时执行；
 - **after_success**：script 阶段成功时执行；
 - **before_deploy**：deploy 步骤之前执行；
 - **after_deploy**：deploy 步骤之后执行；
 - **after_script**：script 阶段之后执行。

完整的生命周期，从开始到结束是下面的流程。

 1. before_install；
 2. install；
 3. before_script；
 4. script；
 5. after_success or after_failure；
 6. before_deploy；
 7. deploy；
 8. after_deploy；
 9. after_script。

### 运行项目

将`.travis.yml`文件push至Git仓的根目录下，至此持续集成项目构建完成，以后每次推送代码到仓库后都将会自动构建项目。
比如，本例中，我们修改`test/add.js`:
```javascript
function add(x, y) {
    console.log(x + y);
    return x + y;
}

module.exports = add;
```
然后将修改的代码push至Git仓:
```
git add .
git commit -m 'test'
git push
```

上传结束后，我们会在travis-ci.org上看到以下信息：
![result-1](result-1.png)
这说明我们的持续集成项目是成功的。
此时，我们可以在github上的gh-pages上查看结果：
![result-3](result-3.png)

然后我们再修改`test/add.test.js`：
```javascript
var add = require('./add.js');
var expect = require('chai').expect;

describe('加法函数的测试', function () {
    it('1 加 1 应该等于 2', function () {
        expect(add(1, 1)).to.be.equal(3);
    });
});
```
然后再次push至Git仓库，此时在travis-ci.org上会出现如下信息：
![result-2](result-2.png)
此时，代码没有通过测试，所以不能执行`after_success`钩子。

<footer>
<hr/>
![footer](http://dwpblog.site/images/footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>