---
title: Flutter 入坑记（一：环境搭建）
date: 2019-08-02 16:59:45
tags: ['flutter']
categories: 'flutter'
---

![此处输入图片的描述][1]

Flutter是谷歌的移动UI框架，可以快速在iOS和Android上构建高质量的原生用户界面。 Flutter可以与现有的代码一起工作。在全世界，Flutter正在被越来越多的开发者和组织使用，并且Flutter是完全免费、开源的。

<!--more-->

## 安装

这里以 `win10` 系统为例

### 下载 `Flutter` 安装包

```
git clone -b stable https://github.com/flutter/flutter.git
```

### 配置环境变量

`控制面板>用户帐户>用户帐户>更改我的环境变量`

变量名|变量值
---|---
PUB_HOSTED_URL|https://pub.flutter-io.cn
FLUTTER_STORAGE_BASE_URL|https://storage.flutter-io.cn
Path|追加变量 `安装包路径\flutter\bin`

![此处输入图片的描述][2]

### 安装依赖

检测是否需要安装任何依赖项来完成安装
```
flutter doctor
```

![此处输入图片的描述][3]

根据提示进行处理
1. 安装 `Android Studio`

提示的下载路径需要翻墙，这里提供一个不用翻墙的下载路径 [Android studio中文网][4]

下载好后打开直接点击下一步安装

![此处输入图片的描述][5]

点击 `cancel`
![此处输入图片的描述][6]

![此处输入图片的描述][7]

安装好后，进入 `Android studio` ，点击 `Plugins` ,搜索 `Flutter` 进行安装

![此处输入图片的描述][8]

![此处输入图片的描述][9]

2. 安装 `Flutter extension`

访问 [Flutter extension][10] 进行安装

安装完成后再运行 `flutter doctor` 进行检测

![此处输入图片的描述][11]

按提示执行命令，然后一直输入 `y` 即可
```
flutter doctor --android-licenses
```

再运行 `flutter doctor` 检测

![此处输入图片的描述][12]

### 创建应用

重新打开 `Android studio` ，选择 `Start a new Flutter project`

![此处输入图片的描述][13]

![此处输入图片的描述][14]

设置项目名称和存储位置

![此处输入图片的描述][15]

![此处输入图片的描述][16]

### 运行应用

配置模拟器

![此处输入图片的描述][17]

![此处输入图片的描述][18]

选择一个模拟器设备

![此处输入图片的描述][19]

选择一个镜像，下载

![此处输入图片的描述][20]

启动应用（首次启动时间较长）

![此处输入图片的描述][21]

![此处输入图片的描述][22]

## 报错记录

1. 命令行执行 `flutter doctor`
报错：
```
Error: The Flutter directory is not a clone of the GitHub project.
       The flutter tool requires Git in order to operate properly;
       to set up Flutter, run the following command:
       git clone -b stable https://github.com/flutter/flutter.git
```
解决方法:
删除下载的Flutter安装包，执行以下命令获取安装包
```
git clone -b stable https://github.com/flutter/flutter.git
```

2. `Android studio` 安装报错

![此处输入图片的描述][23]

原因：win10 系统启用了 `Hyper-V`
解决方法：

- 管理员身份运行cmd
- 执行 `bcdedit /enum`

![此处输入图片的描述][24]

- 执行 `bcdedit /set {current} hypervisorlaunchtype off`
- 电脑重启

3. `Android studio` 点击 `run` 时报错

```
Error connecting to the service protocol: HttpException: Connection closed before full header was received, uri = http://127.0.0.1:59246/77os7RRzy6g=/ws
```

![此处输入图片的描述][25]

原因：镜像版本过高
解决方法：选择低版本的镜像

![此处输入图片的描述][26]


  [1]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/index.jpeg
  [2]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/path.jpg
  [3]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/doctor.jpg
  [4]: http://www.android-studio.org/
  [5]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_setup_0.jpg
  [6]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_setup_1.jpg
  [7]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_setup_2.jpg
  [8]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_plugins_1.jpg
  [9]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_plugins.jpg
  [10]: https://marketplace.visualstudio.com/items?itemName=Dart-Code.flutter
  [11]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/doctor_2.jpg
  [12]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/doctor_3.jpg
  [13]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_start_1.jpg
  [14]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_start_2.jpg
  [15]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_start_3.jpg
  [16]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_start_4.jpg
  [17]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/manager_1.jpg
  [18]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/manager_2.jpg
  [19]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/manager_3.jpg
  [20]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/manager_4.jpg
  [21]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_start_5.jpg
  [22]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/studio_start_6.jpg
  [23]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/error_2.jpg
  [24]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/error_3.jpg
  [25]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/error_4.jpg
  [26]: https://raw.githubusercontent.com/imdwpeng/photoGallery/master/flutter/error_5.jpg

<footer>
<hr/>
![footer](https://raw.githubusercontent.com/imdwpeng/photoGallery/master/footer.gif)
<p style="textAlign:right;color:#ccc">------------笑对人生，能穿透迷雾；笑对人生，能坚持到底；笑对人生，能化解危机；笑对人生，能照亮黑暗。</p>
</footer>