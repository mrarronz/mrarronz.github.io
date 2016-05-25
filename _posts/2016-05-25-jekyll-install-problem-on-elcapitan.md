---
layout: post
title: Install jekyll on OS X EL Capitan
excerpt: "Just about everything you'll need to style in the theme: headings, paragraphs, blockquotes, tables, code blocks, and more."
categories: Jekyll
tags: [gem installation]
comments: true
---

前不久由于要将Xcode升级到7.3版本，只好将系统升级到OS X最新的EL Capitan。升级后cocoaPods使用出现了问题，在Xcode项目中创建podfile文件使用pod install命令提示command not found。原来是cocoaPods在系统升级后被干掉了，只能重新安装了。

```
$sudo gem install cocoapods
```
这个命令打出后安装仍然有问题，正确的做法是：

```
$sudo gem install -n /usr/local/bin cocoapods
```
OS X EL Capitan系统在安全性和稳定性方面都有所提升，这可能是导致原来安装的gem被干掉的原因，新安装的gem必须指定路径。

