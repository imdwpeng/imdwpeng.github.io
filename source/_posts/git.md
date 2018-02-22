---
title: Git 学习手扎
date: 2017-09-16 03:49:22
tags: git
---

![git](1.jpeg)
<!--more-->

## Git 工作流程

 - 克隆 Git 资源作为工作目录
 - 在克隆的资源上添加或修改文件
 - 如果他人修改了，你可以更新资源
 - 在提交前查看修改
 - 提交修改
 - 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交
 
![git 工作流程][1]

## Git 工作区、暂存区和版本库
![git工作区][2]
工作区：即你的电脑里能看到的目录，平时开发代码的地方
暂存区：保存临时改动
版本库：指向最后一次提交的结果

## Git 基本操作
### git init
使用当前目录作为Git仓库，我们只需要初始化
```
git init
```

该命令执行完成后会在当前目录生成一个.git目录（该目录可能是隐藏状态）
我们也可以使用指定目录作为Git仓库
```
git init newrepo
```

初始化后，会在newrepo目录下出现一个.git目录，所有Git需要的数据和资源都存放在这个目录中。

### git clone
使用git clone拷贝一个Git仓库到本地，让自己能够查看该项目，或者进行修改
例如，我们克隆Github上的项目（https://github.com/imdwpeng/gittest.git）
```
git clone https://github.com/imdwpeng/gittest.git
```

克隆完成后，会在目录下生成一个gittest目录

### git pull
从远程仓库更新至本地
```
git pull origin dev   // 取回远程仓库的分支dev，并与本地当前分支合并
```

### git add
将工作区文件添加至暂存区
```
touch test.txt    // 新增文件 test.txt
git add test.txt    // 添加文件test.txt至暂存区
```
如果添加所有文件，则使用 `git add .` 指令。

### git status
查看上次提交后是否有修改
加 -s 参数，以获得简短的结果输出，如果没加该参数，则会输出详细的内容
```
$ git status

  On branch master
  Your branch is up-to-date with 'origin/master'.
  Changes to be committed:
     (use "git reset HEAD <file>..." to unstage)

	    new file:   test.txt
	  
$ git status -s

  A test.txt    // A-->添加至暂存区后又有改动
```

### git diff
查看已写入缓存与已修改但尚未写入缓存的改动之间的区别
先在test.txt输入以下内容
>测试数据
测试数据
测试数据

然后执行 git diff
```
$ git diff

  diff --git a/test.txt b/test.txt
  index e69de29..3e4bb98 100644
  --- a/test.txt
  +++ b/test.txt
  @@ -0,0 +1,3 @@
  +测试数据
  +测试数据
  +测试数据
  \ No newline at end of file
```

### git commit
将暂存区的内容添加至仓库中
有 `-m`、`-am`两种提交方式：

 - **git commit -m '...'**：修改test.txt后，必须先git add之后再执行该命令，才能提交test.txt至仓库中；
 - **git commit -am '...'**：如果之前test.txt已git add过了，则在修改test.txt之后，**无需**再次git add，直接执行该命令，
```
$ git commit -m 'first commit'

  [master bb87244] first commit
   1 file changed, 0 insertions(+), 0 deletions(-)
   create mode 100644 test.txt
```

### git reset HEAD
用于取消已缓存的内容
修改test.txt
>测试数据
测试数据
测试数据
测试数据

然后，提交至暂存区，并取消test.txt的缓存
```
$ git add .
$ git reset HEAD -- test.txt  // 取消test.txt的缓存

  Unstaged changes after reset:
  M	test.txt

$ git commit -m '修改'  // 此时不会提交test.txt

  On branch master
  Your branch is ahead of 'origin/master' by 1 commit.
    (use "git push" to publish your local commits)
  Changes not staged for commit:
  	  modified:   test.txt

  no changes added to commit
  
$ git commit -am '修改提交'  // 此时才会提交test.txt

  [master 0ca7706] 修改
   1 file changed, 3 insertions(+)
```

### git push
将本地分支的更新推送至远程仓库中，格式与git pull类似
```
git push origin dev   // 将本地的 `dev` 分支推送到 `origin` 主机的 `dev` 分支
```

## Git 分支管理
使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。
### git branch

 - 不带参数：列出分支
```
$ git branch 

  * master   // * --> 当前分支
```

 - 带分支名称：创建分支
```
$ git branch dev   // 创建dev分支
$ git branch       // 查看分支信息

  * master
    dev
```

- 带 `-d` 参数：删除分支
```
如果有 `master` 和 `dev` 两个分支
$ git branch -d dev

  Deleted branch dev (was 0ca7706).
```
注：不能直接删除当前分支，须切换至其他分支后才能删除该分支

### git checkout

-  不带 `-b` 参数：切换分支
```
$ git checkout dev    // 切换至dev分支
  
  Switched to branch 'dev'

$ git branch          // 查看分支

  * dev 
    master
```

- 带 `-b` 参数：创建并切换分支
```
$ git checkout -b test

  Switched to a new branch 'test'
  
$ git branch   

    dev
    master
  * test
```

### git merge
合并分支
```
$ git checkout -b source    // 创建并切换至source分支

  Switched to a new branch 'source'
```
新建 source.txt
```
$ ls

  README    text.txt    source.txt

$ git commit -am '创建source分支并新建source.txt文件'   // 将工作区中的内容添加至暂存区，然后添加至仓库

  [source d74eb73] 创建source分支并新建source.txt文件
   1 file changed, 2 deletions(-)
   delete mode 100644 test2.txt
   
$ git checkout master    // 切换至master分支

  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.
  
$ git merge source    // 将source分支内容合并到master中

  Updating 4c152df..9009609
  Fast-forward
  source.txt | 1 +
  test2.txt  | 2 --
  2 files changed, 1 insertion(+), 2 deletions(-)
  create mode 100644 source.txt
  delete mode 100644 test2.txt
  
$ git push               // 上传
...
```
上述例子就实现了分支合并到主干的一个过程
注：

 - 对于需要合并的分支，必须先执行commit指令
 - 分支A合并到分支B，需先checkout至分支B下，然后再merge分支A

## Git 查看提交历史
用 `git log` 命令查看提交历史
```
$ git log

  commit 9009609f116b5d0ee27a809a60201d4e12e3b2ae
  Author: dwp <dwp@chengxu-mac.local>
  Date:   Mon Oct 16 17:02:24 2017 +0800

      asd

  commit d74eb732dad792a20cb5a77aba2aae36a660d171
  Author: dwp <dwp@chengxu-mac.local>
  Date:   Mon Oct 16 16:54:31 2017 +0800

      创建source分支并新建source.txt文件

  commit 4c152df9550b34b133cc0cee079f189bd2052e71
  Merge: e67e18a fcebe30
  Author: dwp <dwp@chengxu-mac.local>
  Date:   Mon Oct 16 16:46:12 2017 +0800

      Mhahahahahahaha
      jjj
      erge branch 'dev'
  ...
```
可选参数：

 - `--oneline`：查看历史纪录的简洁版本；
 - `--graph`：查看历史中什么时候出现了分支、合并；
 - `--reverse`：逆向显示所有日志；
 - ...

## git远程仓库
### 添加远程库
 **1. 配置密钥**
首次以Github为远程仓库时，需通过SSH密钥加密
生成SSH key
```
$ ssh-keygen -t rsa -C "xxxx@xxx.com"    
```
`xxxx@xxx.com` 为你在github上注册的邮箱，然后按提示输入密码，一路回车就行。成功的会在~/下生成.ssh文件夹，进去，打开 `id_rsa.pub` ，复制里面的 key。
回到github上，进入settings
![settings][3]
然后选择SSH and GPG keys，点击 New SSH key。
![new SSH key][4]
然后设置title（随便填），Key是之前 `id_rsa.pub` 复制的key，然后Add SSH key就可以了
![new][5]

**2.新建远程仓库**
点击 New repository ，新建仓库
![New repository][6]
然后，设置远程仓库名（Repository name），点击 Create repository即可。
![此处输入图片的描述][7]
创建成功后，显示以下信息
![此处输入图片的描述][8]
以上信息告诉我们可以从这个仓库克隆出新的仓库，也可以把本地仓库的内容玉松到Github仓库。

### 连接远程仓库
在本地的仓库下，进入一个目录，运行如下命令：
```
echo "# gittest" >> README.md      /* 创建文件 */
git init    /* 初始化git环境，生成一个.git隐藏文件夹及其内容 */
git add README.md    /* 添加变更过的文件，如需添加全部，则执行git add . */
git commit -m "first commit"    /* 提交到缓冲区 */
git remote add origin https://github.com/imdwpeng/gittest.git    /* 将本地仓库连接至远程服务器，只需执行一次 */
git push -u origin master    /* 将 master 分支提交至服务器 */
```
刷新Github，我们就可以看到README.md文件已经上传至Github上了。

## 常见报错及处理
- error:src refspec master does not match any

    原因：引起该错误的原因是，目录中没有文件，空目录是不能提交上去的
    
    解决方法：
```
touch README
git add README 
git commit -m 'first commit'
git push origin master
```

- fatal: remote origin already exists. 

    原因：已将本地仓库和远程仓库绑定
    
    解决方法：
```
// 先删除本地绑定的远程 Git 仓库
git remote rm origin

// 再次绑定远程仓库
git remote add origin git@github.com:FBing/java-code-generator
```

- xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun

    原因：据说苹果每个版本的更新都会有这样的问题，原因是每次安装新的更新后，Xcode都被卸载了。。。。
    解决方法：通过终端重新安装的Xcode命令行工具使用
```
xcode-select --install
```

<footer>
<hr/>
![footer](footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>

  [1]: http://imdwpeng.b0.upaiyun.com/git/git-80375749051507889668206.jpg
  [2]: http://imdwpeng.b0.upaiyun.com/git/123-94318092011508120268646.png
  [3]: http://imdwpeng.b0.upaiyun.com/git/QQ20171016-175344-54313686851508147655605.png
  [4]: http://imdwpeng.b0.upaiyun.com/git/QQ20171016-175604-94906306911508147772188.png
  [5]: http://imdwpeng.b0.upaiyun.com/git/QQ20171016-175724-71362702271508147851334.png
  [6]: http://imdwpeng.b0.upaiyun.com/git/QQ20171016-180150-11769944691508148129483.png
  [7]: http://imdwpeng.b0.upaiyun.com/git/QQ20171016-180521-58127884781508148332247.png
  [8]: http://imdwpeng.b0.upaiyun.com/git/QQ20171016-180706-59537110931508148434327.png
