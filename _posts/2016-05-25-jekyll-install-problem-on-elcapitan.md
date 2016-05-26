---
layout: post
title: Install jekyll on OS X EI Capitan
excerpt: "Just about everything you'll need to style in the theme: headings, paragraphs, blockquotes, tables, code blocks, and more."
categories: Jekyll
tags: [gem installation]
comments: true
---

前不久由于要将Xcode升级到7.3版本，只好将系统升级到OS X最新的EL Capitan。升级后cocoaPods使用出现了问题，在Xcode项目中创建podfile文件使用pod install命令提示command not found。原来是cocoaPods在系统升级后被干掉了，只能重新安装了。

```
sudo gem install cocoapods
```
这个命令打出后安装仍然有问题，正确的做法是：

```
sudo gem install -n /usr/local/bin cocoapods
```


在安装jekyll的时候也出现了问题，这里主要记录下当时的操作，以便以后遇到问题能快速定位，具体错误差不多都忘了。

当执行sudo gem install jekyll命令时总是提示安装不成功，具体错误提示忘记了，大概是:

```
ERROR: Failed to build gem native extension....
```
然后搜索了半天，结合错误提示，猜测可能是ruby的原因，于是按照各路网友的做法，使用RVM安装ruby。RVM是安装成功了，但是ruby还是安装不成功，mac系统已经自带ruby了，所以用RVM安装ruby个人觉得其实也没必要。直到找到这个[Jekyll安装教程](http://www.zhanxin.info/jekyll/2013-08-07-jekyll-doc-installation.html)之后，又重新试着使用Homebrew来更新ruby。在安装过程中出现问题，按照提示执行brew link xxx(这里都不记得了)，执行之后还是没成功。由于homebrew是我之前安装过的，所以决定将它卸载，重装一遍。

卸载Homebrew，按照以下命令一步步完成

```
cd `brew --prefix`
rm -rf Cellar
brew prune
rm `git ls-files`
rm -r Library/Homebrew Library/Aliases Library/Formula Library/Contributions
rm -rf .git
rm -rf ~/Library/Caches/Homebrew
```

[安装Homebrew](http://brew.sh/)

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
这才是正确的方式！

接着执行以下命令更新ruby

```
brew install ruby
```

```
ruby -v
```
ruby成功安装，最新版本是2.3.1p112

然后安装jekyll

```
sudo gem install jekyll
```
顺利安装完成，可以使用命令校验一下

```
jekyll -v
```

cd到自己的github page仓库下，执行

```
jekyll server
```
启动成功，在浏览器地址栏输入

```
http://127.0.0.1:4000/  （或者localhost:4000）
```
成功加载出blog主页。


