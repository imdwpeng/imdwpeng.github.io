---
title: 【Git】git hooks 自动化部署代码
date: 2018-05-09 20:56:02
tags: git
categories: git
---

本文主要介绍如何用`git hooks`来进行自动化部署发布我们的代码，省去了我们发布前一系列不必要的打包发布等繁琐步骤。

![title](index.jpeg)

<!--more-->
## 背景
用`git`管理代码时，每次发布时，我们都需要经过以下步骤：

 1. 将本地代码`push`至远程仓库；
 2. 然后再`ssh`到服务器上进行`git pull`操作；
 3. 对代码进行编译打包压缩发布等吧啦吧啦各种无趣的操作；

这很是繁琐，本着程序猿（媛）能躺着就不坐着的基本素养，我们是不是能够自动去监测我们的远程仓库，当我们每次`push`之后，它就自动去实现代码的更新编译部署等一系列功能呢？答案是，当然可以。
下面就开始漫漫踩坑路。

## 自动部署理论
首页，我们要明确项目代码的分布情况：开发者电脑的本地仓库，git服务器上的远程仓库，web服务器上的本地仓库（我们浏览访问的就是这里的代码）。
所谓自动部署，就是当我们将代码从本地仓库push到远程仓库时，自动把代码部署到web服务器上的本地仓库，实现开发者电脑的本地仓库和web服务器上的本地仓库代码同步。

## git hooks
git有一个神器：[git hooks](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)，没了解过的话可以去官网了解下。
它的功能是能在特定的重要动作发生时触发自定义脚本。 它有两组这样的钩子：客户端的和服务器端的。 客户端钩子由诸如提交和合并这样的操作所调用，而服务器端钩子作用于诸如接收被推送的提交这样的联网操作。 你可以随心所欲地运用这些钩子。
这里我们主要用到服务端的钩子：`post-receive`。

## 环境搭建
本例服务器环境：
&emsp;&emsp;&emsp;&emsp;主机：111.111.111.111
&emsp;&emsp;&emsp;&emsp;用户：root
    
约定（可自定义）：
&emsp;&emsp;&emsp;&emsp;裸仓库建在`/home/workspace`下
&emsp;&emsp;&emsp;&emsp;web本地仓库建在`/home/www`下
&emsp;&emsp;&emsp;&emsp;所属用户：git

### Git服务器搭建
1.连接至服务器
ssh连接至服务器
```
ssh root@111.111.111.111
```
![hook-1](hook-1.png)

2.创建git用户
我们创建一个`git`用户，专门用来运行`git`服务
```
sudo adduser git   创建用户
sudo passwd git    设置密码
```

3.创建裸仓库
我们在`/home/workspace`文件夹下创建Git裸仓库
```
mkdir /home/workspace
cd /home/workspace
git init --bare layercake.git
```
![hook-2](hook-2.png)

### 配置Git Hooks
进入`/home/workspace/layercake.git`文件夹，使用`vi post-receive`创建一个脚本。
```
cd /home/workspace/layercake.git/hooks
vi post-receive
```
当我们本地`git push`之后就会触发服务器上的`post-receive`脚本
`post-receive`内容如下：
```bash
#!/bin/sh
 
unset GIT_DIR
cd /home/www/layercake
git pull
```
这是一个简单的脚本，文章结尾有更复杂的`post-receive`配置，可以根据不同分支进行不同操作。

再配置`post-receive`权限
```
chmod +x post-receive
```

### 创建代码仓库
然后我们在`/home/www`下创建一个代码仓库，关联先前创建的`layercake`仓库
```
mkdir /home/www
cd /home/www
git clone /home/workspace/layercake.git
```
![hook-3](hook-3.png)

### 配置公钥
如不配置公钥，每次本地`push`时都会要求输入服务器密码，不胜其烦，故我们配置下公钥来跳过这一步骤。
将本机的公钥加入至`/home/git/.ssh/authorized_keys`中，`authorized_keys`文件里包含所有该项目的开发者们的ssh公钥。
如果没有该文件则创建它：
```
mkdir /home/git
cd /home/git
mkdir .ssh
chmod 755 .ssh
touch .ssh/authorized_keys
chmod 644 .ssh/authorized_keys
```

如本地没有创建过密钥，则执行`ssh-keygen -t rsa -C xx`来创建自己本地的密钥，`xx`为自己的邮箱，然后按提示操作即可。

此时本地会生成`.ssh`文件夹，打开里面的`id_rsa.pub`文件，复制内容粘贴至上面说的`authorized_keys`文件中就即可。

然后设置下仓库所属用户（git）
```
sudo chown -R git:git /home/workspace/layercake.git
sudo chown -R git:git /home/www/layercake
```

### 本地克隆仓库
在本地电脑上，克隆服务器上的git仓库
```
 git clone git@111.111.111.111:/home/workspace/layercake.git
```
按照提示输入服务器密码即可。
![hook-4](hook-4.png)

到此，全部配置已完成，以后每次本地仓库`push`至远程仓库时，都会触发`post-receive`脚本，从而更新`/home/www/layercake`仓库代码。

## 完整版`post-receive`脚本配置
功能：

1.根据不同分支进行不同操作：
我们可以定义不同分支，当该分支push后触发对应操作，如`dev_debug`分支更新时触发测试库代码更新。

2.跨服务器操作：
当我们的web服务器代码和服务器上的本地代码位于不同服务器时，通过`scp`命令进行文件或目录的复制。

每次`scp`命令复制时，都需要输入接收服务器的密码，这不是程序猿（媛）的风格，所以我们也采用公钥配置方式来免去输入密码这一步。

假如web服务器(F2)：
&emsp;&emsp;&emsp;&emsp;主机：222.222.222.222
&emsp;&emsp;&emsp;&emsp;用户：root

远程仓库(F1)：
&emsp;&emsp;&emsp;&emsp;主机：111.111.111.111
&emsp;&emsp;&emsp;&emsp;用户：git

**F1服务器操作：**

`ssh`连接至F1，创建密钥：

```
ssh git@111.111.111.111   # 连接至服务器，按提示输入密码

ssh-keygen -t rsa   # 创建密钥（如F1服务器还未创建密钥），一路回车，无须设置密码
```

在`.ssh`目录下会生成`id_rsa`（私钥）和 `id_rsa.pub`（公钥）两个文件，复制`id_rsa.pub`中的内容。

**F2服务器操作：**

连接上F2服务器，将F1公钥添加至`/root/.ssh/authorized_keys`中即可。

```
ssh root@222.222.222.222  # 连接至服务器，按提示输入密码
vi /root/.ssh/authorized_keys
// 添加F1的公钥，保存退出

chmod 600 /root/.ssh/authorized_keys  # 设置文件权限600
```

有时候连接服务器会出现以下错误：

```
Are you sure you want to continue connecting (yes/no)?
```

我们是脚本运行，所以不好去输入`yes`，解决方法有两种：

 1.在F1的`post-receive`脚本中使用`ssh -o`的参数进行设置：
```
ssh -o StrictHostKeyChecking=no root@222.222.222
```

2.修改F2服务器中`/etc/ssh/ssh_config`：
将其中的`# StrictHostKeyChecking ask`改成`StrictHostKeyChecking no`。

本例采用第二种方法。

以下是本例中完整的`post-receive`配置：

```bash
#!/bin/sh

###### 用户配置区 开始 ######

# web服务器本地仓库目录
DEPLOY_PATH="/home/www/layercake"

# 测试库分支、测试服务器
dev_debug="dev_debug"
debug_path="root@222.222.222.222:/home/www"

# 灰度分支、灰度服务器
dev_test="dev_test"
test_path="root@222.222.222.222:/home/www"

# 正式库分支、正式服务器
dev_prod="master"
prod_path="root@114.55.67.225:/home/www"

###### 用户配置区 结束 ######

path="none"

# 去掉Git默认的环境变量
unset GIT_DIR

while read oldrev newrev refname
do
    # 当前分支
    branch=$(git rev-parse --symbolic --abbrev-ref $refname)
    echo "======= start ======="
    echo "Target branch: $branch"
    # 指定到项目文件夹
    cd $DEPLOY_PATH
    # 创建分支（已存在会提示已存在，继续往下执行）
    git branch $branch
    # 指定到该分支
    git checkout $branch
    # 拉取代码
    git pull origin $branch:$branch

    # 根据分支判断上传环境
    case $branch in
        $dev_debug)
            envName="测试库"
            path=${debug_path}
            ;;
        $dev_test)
            envName="灰度"
            path=${test_path}
            ;;
         $dev_prod)
            envName="正式库"
            path=${prod_path}
            ;;
        *)
            echo "fail!"
            ;;
    esac

    # 上传至测试环境、灰度、正式环境
    if [ $path != "none" ]
    then
        # -r 拷贝文件夹
        # -v 显示详情
        rsync -r -v $DEPLOY_PATH $path
        echo "publish to $envName..."
    fi
    echo "======= end ======="
done
```

<footer>
<hr/>
![footer](http://dwpblog.site/images/footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>