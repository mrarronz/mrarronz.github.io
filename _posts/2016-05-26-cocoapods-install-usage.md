---
layout: post
title: CocoaPods的安装和使用
excerpt: "CocoaPods的安装和使用，如何创建podspec并push到cocoaPods仓库"
categories: CocoaPods
tags: [CocoaPods]
comments: true
---

## 安装

CocoaPods可以使用Mac自带的RubyGems来安装，但是由于ruby的软件源rubygems.org被天朝屏蔽，只能切换为淘宝的rubygems镜像。

```r
XXX-MacBook-Pro:~ XXX$ gem sources -l
*** CURRENT SOURCES ***

https://ruby.taobao.org/
```
移除当前的rubygems，替换为淘宝的

```
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
gem sources -l
```
可以用以下命令对gem 进行更新：

```
sudo gem update --system
```
然后就可以安装cocoaPods了

```
sudo gem install cocoapods
```
Max系统升级到OS X EI Capitan之后改为

```
sudo gem install -n /usr/local/bin cocoapods
pod setup
```
<br/>

## 使用

1、新建Podfile

cd到项目的根目录下

```
touch Podfile
```
打开Podfile，在Podfile中引用第三方库

```
vim Podfile
```

```r
platform :ios, '6.0'   
pod 'AFNetworking', '~> 3.0'
```
ESC退出编辑，ZZ关闭文件

2、安装第三方库

```
pod install
```
更新时podspec会更新本地仓库，这个过程耗时比较长，可以用以下命令

```
pod install --verbose --no-repo-update (注：--verbose可以不加，加上是为了打出更多调试信息)
```

执行完后，项目根目录下会出现Podfile.lock，pods，后缀名为.xcworkspace的项目文件。以后打开项目就只需要双击xcworkspace文件。
<br/>

3、更新pod

编辑Podfile文件，加入其它第三方库的引用方式，再执行pod update。在这个过程中需要注意第三方库之间的引用关系，以免造成pod update出错的情况。

```
pod update --verbose --no-repo-update
```

4、多个target的使用方式

推荐方式：不同的target使用不同的第三方依赖配置，如下：

```r
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '6.0'

target :TargetName1 do
    pod 'AFNetworking'
    pod 'MBProgressHUD'
end

target :TargetName2 do
    pod 'MBProgressHUD'
end
```
<br/>

## 创建podspec文件，为自己的项目增加pod支持

1、将自己的源码上传到github仓库再clone到本地，如果仓库有代码就直接clone

2、创建podspec文件

cd到项目的根目录，在终端执行以下命令

```
pod spec create XXXX(项目名称)
```
这时候本地仓库就生成了一个podspec文件

3、配置podspec文件

用vim或者编辑器打开.podspec文件进行编辑，以我的项目[NSStringCategoryKit](https://github.com/mrarronz/NSStringCategoryKit)为例

```r
Pod::Spec.new do |s|
  s.name         = "NSStringCategoryKit"
  s.version      = "0.0.1"
  s.summary      = "A NSString category collections."
  s.description  = <<-DESC
 A category collections for NSString class. All the methods in category are often used in app development.
                   DESC
  s.homepage     = "https://github.com/mrarronz/NSStringCategoryKit"
  s.license      = "MIT"
  s.author       = { "mrarronz" => "zhuhao1340@xxx.com" }
  s.platform     = :ios, "7.0"
  s.source       = { :git => "https://github.com/mrarronz/NSStringCategoryKit.git", :tag => "0.0.1" }
  s.source_files  = "NSStringCategoryKit"， "NSStringCategoryKit/**/*.{h，m}"
  s.public_header_files = "NSStringCategoryKit/**/*.h"
  s.frameworks = "Foundation", "UIKit", "CoreImage"
  s.requires_arc = true
end
```

4、验证podspec文件

在上传podspec文件之前，先验证一下看是否有错误

```
pod lib lint
```
如果出现passed validation的提示，就说明验证通过了，如果出现错误，可以根据错误消息来更改配置文件。

```
pod lib lint --verbose
```
使用以上命令验证时会给出更详细的提示信息，根据这个错误信息来修改配置文件

5、打tag上传podspec文件

```r
git tag -m "first release" "0.0.1"
git push --tags
```
注意，这里必须要给项目打上tag，作为一个版本来发布。然后就是把podspec上传到cocoapods官方库了。以前可能需要用clone，pull request的方式提交，耗时非常久，现在只需要参照官方的做法

```r
pod trunk register orta@cocoapods.org 'Orta Therox' --description='macbook air'
```
将邮箱，用户名和description改为自己的，执行之后，这个邮箱会收到一封来自cocoaPods官方的邮件，确认之后就注册完成了

然后使用

```
pod trunk me
```
这个命令来查看当前你的账号的会话状况，这个是跟你的设备相关的

cd到你的podspec所在目录，执行以下命令开始提交podspec文件到cocoaPods官方库，这里还是以我的项目为例

```
pod trunk push NSStringCategoryKit.podspec
```
这个过程等待时间比较长，大概一个小时左右，成功之后执行

```
pod setup
```
更新一下本地仓库，再执行

```
pod search NSStringCategoryKit
```
就可以找到项目了。

6、使用自己的pods项目

在Xcode项目的Podfile文件中按照上面的cocoapods的使用方法配置Podfile，例如

```r
platform:ios, '7.0'   
pod 'NSStringCategoryKit'
```
然后执行更新操作就可以了。

如果没有将自己的podspec文件上传，也可以在项目中进行使用，这时需要指定github的项目路径和tag

```r
platform :ios, '7.0'
pod 'NSStringCategoryKit', :git => 'https://github.com/mrarronz/NSStringCategoryKit.git', :tag => '0.1.0'
```
