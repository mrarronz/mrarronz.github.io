---
layout: post
title: SVN命令汇总和基本用法
excerpt: "SVN的使用"
categories: 版本管理
tags: [Subversion]
comments: true
---

本文内容来自于以下文章，著作权属于原作者。本文在原作的基础上加以整理修改而成，仅用于笔记参考学习。

>* [svn常用命令详解（很全，很实用）](http://blog.csdn.net/prettyshuang/article/details/38421205?utm_source=tuicool&utm_medium=referral)
>* [(转)SVN常用命令用法说明](http://blog.csdn.net/lddongyu/article/details/5493862)
>* [SVN 主干(trunk)、分支(branch )、标记(tag)](http://panfuy.iteye.com/blog/1278865)

<br/>

## Subversion的命令

在命令行输入`svn help`可以查看svn的所有命令

<br/>

## 命令用法

1、svn add

添加文件或目录到你的working copy(`以下简称WC`)，打上新增标记A。这些文件会在下一次你提交WC的时候提交到svn服务器.

```
svn add test.h (添加一个文件)
svn add *.h    (添加当前目录下所有.h文件)
```

2、svn blame(praise, annotate, ann)

显示某个在版本管理下的文件的每一行的最后修改版本和作者

```
svn blame test.h
svn blame --xml test.h  (加上xml参数可以以xml格式显示每一行的属性)
```

3、svn cat 

不checkout而查看输出特定文件或URL的内容

```
svn cat file.cpp
svn cat file.cpp -r 2              (显示版本号为 2 的file.cpp内容)
svn cat file.cpp --revision HEAD   (显示最新版本的file.cpp内容)
svn cat svn://127.0.0.1/test/readme.txt  (文件全路径)
```
4、svn changelist(cl)

可以将WC中的文件从逻辑上分组.

* changelist CLNAME PATH...
* changelist --remove PATH...

```
svn cl testChangelist file1.h file2.h file3.h  (将file1.h等三个文件加入名叫testChangelist的changelist)
svn commit --changelist testChangelist -m "create new changelist"  (将testChangelist下的所有文件提交)
```
5、svn checkout

copy出一份文件

```
svn co [svn路径]
svn co -r 200  (checkout出版本号为200的项目)
```
6、svn cleanup

递归的清理WC中过期的锁和未完成的操作。

```
svn cleanup
```
7、svn commit(ci)

将更改提交到仓库

```
svn commit -m "Update file." (必须要加上提交的注释)
```
8、svn copy(cp)

* copy操作可以从WC到WC；WC到URL；URL到 WC；URL到URL。现在SVN 只支持同一个仓库内文件的拷贝，不允许跨仓库操作。
* copy命令是创建分支和标记的常用方式。copy到url的操作隐 含了提交动作，所以需要提供log messages。

```
svn copy path1 path2 -m "This is the commit message" (path1 和 path2是同一个仓库内的项目路径)
```
9、svn delete(del, remove, rm)

删除文件

```
svn delete file1.h  (commit的时候才会将仓库中的file1.h删除)
svn delete file://192.168.1.111/svn/file1.h  (删除仓库中的文件)
```
10、svn diff(di)

用来比较并显示修改点

```
svn diff    (用来显示WC基于最近一次更新以后的所有的本地修改点)
svn diff -r 301 bin   (比较本地WC和版本301中的bin目录的修改点)
svn diff -r 3000:3500 file://var/svn /repos/myProject/trunk   (比较库里主干3000版和3500版的差异)
```
11、svn export

导出一个干净的目录树，不包含所有的版本管理信息。可以选择从URL或WC中导出

```
svn export file://var/svn/repos my-export  (导出到my-export目录)
```
12、svn help(?, h)

13、svn import

导入本地一个目录到库中。但是导入后，本地的目录并不会处于版本管理状态

```
svn import -m "message" path
```
14、svn info

查看当前WC或目录的信息

15、svn list(ls)

显示目标下的文件和目录列表

```
svn ls --verbose path (verbose可以不加，加上是为了显示详细信息)
```
16、svn lock

对目标获得修改锁。如果目标已被其他用户锁定，则会抛出警告信息。 用--force参数强制从其他用户那里获得锁

```
svn lock filename.h
```
17、svn log

查看仓库修改时的log信息，包括add，modified，delete，replace， merge

```
svn log
svn log -r 200 (查看指定版本号的提交log)
```
18、svn merge

合并两个受控源的不同之处，存放到一个WC里。

```
svn merge --reintegrate http://svn .example.com/repos/calc/branches/my-calc-branch  (合并分支上的改变项到WC，往往用于分支合并到主干)
svn merge -r 156:157 http://svn .example.com/repos/calc/branches/my-calc-branch   (将制定URL版本156到157的所有更新合并到WC)
```
19、svn mergeinfo

查看merge操作的信息

```
svn mergeinfo http://svn .example.com/repos/calc/branches/my-calc-branch
```
20、svn mkdir

在WC或库路径创建目录

```
svn mkdir DirectoryName
```

21、svn move(mv, rename, ren)

等同于svn copy命令跟个svn delete命令。WC到URL的重命名是不被允许的。

```
svn move foo.c bar.c  (将foo.c改名成bar.c)
```
22、svn patch

[svn生成patch和打（导入）patch文件的方法](http://blog.csdn.net/ameyume/article/details/7766722)

23、svn propdel (pdel, pd)

* svn propedit PROPNAME TARGET...
* propedit PROPNAME --revprop -r REV [TARGET]

从受控文件，目录等删除属性。第二种是删除某个指定版本上的附加属性

```
svn propdel svn :mime-type someFile    (从someFile上移除svn :mime-type这个属性)
```
24、svn propedit (pedit, pe)

* svn propedit PROPNAME TARGET...
* svn propedit PROPNAME --revprop -r REV [TARGET]

修改属性

```
svn propedit svn :keywords  file.c  (修改file.c上的svn :keywords属性)
```

25、svn propget (pget, pg)

* svn propget PROPNAME [TARGET[@REV]...]
* svn propget PROPNAME --revprop -r REV [URL]

从文件，目录或版本取得指定属性的值

```
svn propget svn :keywords file.c   (从file.c中取得svn :keywords属性的值)
```
26、svn proplist (plist, pl)

* svn proplist [TARGET[@REV]...]
* svn proplist --revprop -r REV [TARGET]

列出文件、目录或版本上的所有附加属性

```
svn proplist --verbose file.c
```
27、svn propset (pset, ps)

给文件、目录或版本附加属性并赋值

```
svn propset svn :mime-type image/jpeg file.jpg   (给file.jpg附加属性svn :mime-type 其值为image/jpeg)
svn propset --revprop -r 25 svn :log "Journaled about trip to New York." (给版本25补上log message)
```
28、svn resolve

将冲突的文件标记为已解决，并且删掉冲突产生的临时文件。注意这个命令并不是能把冲突解决，解决冲突还是得靠人工，人工解决冲突后，文件的状态还是冲突C状态，需要将其标记为resolved之后才能正常提交

```
svn resolve --accept filePath  (filePath是文件路径，1.5版本后，加上--accept参数，尝试自动处理冲突)
```
29、svn revert

还原WC中所有的本地更改

```
svn revert --depth=infinity  (将整个目录所有文件还原)
svn revert test.h            (将对test.h文件所做的修改还原)
```
30、svn status(stat, st)

输出WC中文件和目录的状态。如果WC提交，这些状态就会同步到仓库里。一般有如下几种状态：

'' 没有修改
A  新增
D  删除
M  修改
R  替代
C  冲突
I  忽略
？ 未添加到版本管理
！ 文件丢失

31、svn switch(sw)

* svn switch URL，将当前WC切换到另一个URL，一般是从trunk工作目录转向tag工作目录，或者从tag转回来，switch的类似update，将switch的目标工作目录的文件更新到本地，一般会产生很多冲突
* svn switch --relocate，将WC连接的服务器地址切换到另一个地址，比如服务器地址由IP地址改为域名的情况，但是项目文件结构都没有变化。

```
svn switch path
svn switch --relocate path1 path2
```
32、svn unlock

```
svn unlock fileName
```
33、svn update(up)

更新本地WC，直接使用svn up命令，如果要指定版本，使用svn up -r 200 (更新到版本号为200的版本)

34、svn upgrade

如果本地svn版本高于WC当前被管理的svn版本，那么就需要使用upgrade命令将WC的svn版本升级。可以理解为将当前WC所处的环境升级，本来是在1.6版本svn的管理之下，现在通过upgrade之后处于1.7版本的svn管理之下了。

<br/>

## 主干、分支和标记

通常我们在项目开发中都会建立主干(trunk)、分支(branch )、标记(tag)来管理项目代码。

一般情况下

* trunk：是用来做主方向开发的，一个新模块的开发，这个时候就放在trunk，当模块开发完成后，需要修改，就用branch。在开发阶段的代码都提交到该目录上。 
* branch：是用来做并行开发的，这里的并行是指和trunk进行比较。
* tag：是用来做一个milestone的，不管是不是发布版本，但都是一个可用的版本。这里，应该是只读的。更多的是一个显示用的，给人一个可读的标记。

比如，3.0开发完成，这个时候要做一个tag，tag_release_3_0，然后基于这个tag做发布。trunk进入3.1的开发，但是3.0发现了bug，那么就需要基于tag_release_3_0做一个分支(branch)，branch_bugfix_3_0，基于这 个branch进行bug修改，等到bugfix结束，做一个tag，tag_release_3_0_1，然后，根据需要决定 branch_bugfix_3_0是否并入主干(trunk)。

branch和tag的创建都是用svn copy命令完成，将trunk目录下的工程copy到对应的branch和tag目录

```
svn cp path1 path2 -m "create branch" (path1表示trunk所在路径，path2表示branch或tag路径)
```