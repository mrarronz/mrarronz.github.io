---
layout: post
title: Carthage的安装和使用
excerpt: "Carthage的安装和使用，怎样给自己的项目创建carthage引用"
categories: Carthage
tags: [Carthage]
comments: true
---

Carthage是一个去中心化的Cocoa依赖管理器，它与CocoaPods的不同在于：

> * Carthage使用xcodebuild来编译框架的二进制文件，但如何集成它们将交由用户自己判断，carthage更加灵活，并且对于我们的项目来说是非侵入性的。我们可以很灵活的管理carthage编译的第三方库。
> * CocoaPods是把第三方库集中管理，默认会自动创建并更新你的应用程序和所有依赖的Xcode workspace，它修改了我们的项目文件，来达到统一管理第三方库的目的。

CocoaChina上有对[carthage的介绍](http://www.cocoachina.com/ios/20141204/10528.html)，本文的目的是将carthage的安装和使用等方法统一整理，作为笔记，便于参考记忆。

<br/>

## 安装Carthage

Carthage可以通过[Homebrew](http://brew.sh/)来安装，但是直接安装之后出现如下问题。

```r
XXX-MacBook-Pro:~ XXX$ brew install carthage
carthage: A full installation of Xcode.app 7.3 is required to compile this software.
Installing just the Command Line Tools is not sufficient.
Xcode can be installed from the App Store.
Error: An unsatisfied requirement failed this build.
```
我的Xcode版本是7.2，安装carthage必须要使用Xcode最新的版本，仅仅只安装Command line tools是不够的。

App store搜索Xcode 7.3安装，等了半天没反应，软件更新提示里面也没有Xcode。看了Xcode下面的评论才知道原来得先把系统升级到最新的EI Capitan之后才会出现Xcode 7.3版本的更新提示。这个没办法了，网速太渣，系统没法升级。只能下载Carthage官方最新的[Release版本](https://github.com/Carthage/Carthage/releases)了.

下载.pkg文件安装之后，在终端中查看：

```
XXX-MacBook-Pro:~ XXX$ carthage version
0.16.2
```
说明安装成功

<br/>

## 使用Carthage

1、创建Cartfile

cd 到项目的根目录下

```
touch Cartfile
```
与CocoaPods的Podfile类似，将第三方库的引用方式写入到Cartfile文件中。以AFNetworking为例

```r
github "AFNetworking/AFNetworking" ~> 3.0
```
保存Cartfile后，执行

```r
XXX-MacBook-Pro:TestCarthageDemo XXX$ carthage update
*** Cloning AFNetworking
*** Downloading AFNetworking.framework binary at "3.1.0"
*** Checking out AFNetworking at "3.1.0"
*** xcodebuild output can be found in /var/folders/nc/r4zs4js928nd2ty6ng64c7sr0000gp/T/carthage-xcodebuild.nVuUQ1.log
*** Building scheme "AFNetworking OS X" in AFNetworking.xcworkspace
*** Building scheme "AFNetworking tvOS" in AFNetworking.xcworkspace
*** Building scheme "AFNetworking watchOS" in AFNetworking.xcworkspace
*** Building scheme "AFNetworking iOS" in AFNetworking.xcworkspace
```
在项目的根目录下会多出Carthage文件夹和Cartfile.resolved文件。

Carthage目录下还有两个文件夹，Build文件夹中存放的是针对各个平台编译好的AFNetworking的framework，Checkouts文件夹中存放的是整个AFNetworking的源代码。

2、在项目中引用framework

（1）打开Xcode项目，在项目设置页面General tab中，找到Linked Frameworks and Libraries，将Build文件夹中对应的AFNetworking.framework添加进来。iOS project就选择iOS文件夹下的framework，其它同理。这一步就把framework添加到项目中了，运行项目，build成功，app启动后却发生如下错误

```r
dyld: Library not loaded: @rpath/AFNetworking.framework/AFNetworking
Referenced from: /Users/XMD001/Library/Developer/CoreSimulator/Devices/1C63D6A2-7EBA-420B-99C4-38C7C1A25911/data/Containers/Bundle/Application/7F3B81EA-9ADF-46DF-9F32-E80F6BED1628/TestCarthageDemo.app/TestCarthageDemo
Reason: image not found
```
还有些工作没做完，接着第（2）步~

（2）在Build Phases设置页面，点击左上角的'+'号，新建"New Run Script Phase"，然后将以下脚本粘贴在文本框中：

```
/usr/local/bin/carthage copy-frameworks
```
然后在下面的“Input files”区域将framework的相对路径添加进来：

```r
$(SRCROOT)/Carthage/Build/iOS/AFNetworking.framework
```
然后再运行项目，成功了！

看[官方的说明](https://github.com/Carthage/Carthage#getting-started)，第（2）步的脚本以work around的形式解决了一个app提交审核时由universal binaries导致的bug。所以这里一定要加上第（2）步的脚本配置。

3、添加framework到Unit Test或framework项目中

由于unit test的General tab中没有Linked Frameworks and Libraries的设置项，framework类型的项目中没有General这个tab，所以必须把carthage编译好的framework添加到Build phase的Link Binaries With Libraries中。在unit test的build settings中找到“Runpath Search Paths”这个选项，如果这一项没有值，那么还需添加以下路径：

```
@loader_path/Frameworks
```

4、更新引用的framework

如果修改了Cartfile（加入了新的framework），或者想要更新现在使用的framework，使用以下命令：

```
carthage update
```
如果只更新某一个framework，可以使用

```
carthage update XXX(framework名字)
```

5、framework之间嵌套依赖

这表示引用的framework之间存在比较多的依赖关系，carthage会自动帮你恢复，framework编译之后需要手动将其添加到项目中

<br/>

## 为自己的framework添加Carthage支持

1、必须使用Dynamic framework

自己的工程类型必须是Dynamic framework，static library是不行的

2、分享 Xcode scheme

Carthage 只构建从 .xcodeproj 分享出来的 Xcode schemes。可以通过运行 

```
carthage build --no-skip-current
``` 
来检测所有的 intended schemes 是否构建成功，然后检查 Carthage/Build 文件夹。如果运行命令的时候，一个重要的 scheme 没有构建成功，打开 Xcode 在构建菜单选择 Manage Schemes （如下图）

![image](/images/carthage/carthage_1.png)

对于需要构建的 scheme 勾选 Shared （如下图）
![image](/images/carthage/carthage_2.png)

示例图片是我的github项目[NSStringCategoryKit](https://github.com/mrarronz/NSStringCategoryKit)，也添加了CocoaPods支持。这个项目最初是以static library的方式创建的，后来为了支持carthage，新建了一个framework的target。如果要添加carthage支持，最好还是创建新的framework项目，再来进行carthage的集成。

3、解决构建失败以及打tag

这部分请查看[官方文档](https://github.com/Carthage/Carthage#resolve-build-failures)，说得非常详细了，不再赘述，上面描述的过程都是亲自实践的。

<br/>

## 总结
通过对CocoaPods和Carthage的使用，两种方式各有优缺点。

> * CocoaPods会更改项目文件，生成新的.xcworkspace文件，对项目具有侵入性。
> * Carthage使用比较方便灵活，但是把源代码clone下来就没有必要了吧，导入的项目多了，clone出来Checkouts文件夹里面源代码一堆。

目前来说个人还是用CocoaPods更多一些，完全不用关心怎么设置library的引用，简单方便。
