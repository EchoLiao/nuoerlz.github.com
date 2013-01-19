---
layout: post
title: "发布 iOS App 到越狱市场"
date: 2012-09-07 17:24
comments: true
categories: iOS jailbreak cydia
---


## 检查 dpkg-deb 工具集是否可用

    $ dpkg-deb --version
        Debian `/sw/bin/dpkg-deb' package archive backend version 1.10.21 (darwin-x86_64).
        This is free software; see the GNU General Public Licence version 2 or
        later for copying conditions. There is NO warranty.
        See dpkg-deb --licence for details.

输入命令后有类似上面的输出, 则 dpkg-deb 命令可用, 否则需要自己安装.
<!--more-->


## install dpkg-deb tools for Mac (Fink)

制作deb文件需要有dpkg-deb命令, Ubuntu/Debian 系统自带此命令, 越狱过的 iPhone
OS 也有此命令, Mac OS X 下需安装Fink, Windows 下需安装 Cygwin.

Fink 的主页是 http://www.finkproject.org .

以下是在 Mac OS X 下的安装过程. 可以参考官网上的[Quick Start][if1].

安装Fink可以使用 dmg 包或源码安装, 为了下载正确的的安装包, 先查看一下当前的系
统版本:
    $ system_profiler | grep "System Version"
          System Version: Mac OS X 10.7.4 (11E2617)
    $ uname -a
        Darwin erlzmatoMacBook-Pro.local 11.4.2 Darwin Kernel Version 11.4.2: Wed May 30 20:13:51 PDT 2012;
        root:xnu-1699.31.2~1/RELEASE_X86_64 x86_64

我的系统版本为 10.7 , 没有对应的 dmg 安装包, 只能通过源码安装了.


[下载源码][if2]:
    $ cd ~/Downloads/
    $ wget http://downloads.sourceforge.net/fink/fink-0.34.4.tar.gz

安装 Fink:
    $ tar xvzf fink-0.34.4.tar.gz
    $ cd fink-0.34.4
    $ sudo ./bootstrap


## 用 ipa 包 制作 deb 包

### 准备目录树和相关文件

创建一个临时目录, 把一些必须文件复制进来, 如: xx.ipa ,
    $ mkdir work
    $ cd work
    $ cp ~/VPlayer*.ipa .
    $ ls
        VPlayer-0.0.5.ipa
	$

解压 ipa 包:
    $ unzip VPlayer-0.0.5.ipa
    $ ls Payload/
     VPlayer.app

准备 deb 包目录树, 创建相关目录, 配置文件和脚本, 并把 *.app 复制到相应目录里,
最终的目录文件结构如下:
    $ ftree .
        ./VPlayer
              |---Applications
              |           |---VPlayer.app
              |---DEBIAN
              |     |---control
              |     |---preinst
              |     |---postinst
              |     |---prerm
              |     |---postrm
              |     |---extrainst_


#### 配置文件

在 DEBIAN 目录下添加 control 文件, 各个关键字的详解见["DEBIAN之control"][if3].
    $ cat > VPlayer/DEBIAN/control
        Package: com.yixia.VPlayer
        Version: 0.0.5
        Name: VPlayer Video Player
        Section: [Utilities]
        Architecture: iphoneos-arm
        Author: VPlayer<VPlayer-ios@yixia.com>
        Maintainer: VPlayer<www.VPlayer.net>
        Homepage: www.VPlayer.net
        Icon: file:///Applications/VPlayer.app/Icon.png
        Installed-Size: 9102
        Priority: Optional
        Essential: no
        Description: The best video player for iOS!
        tag: purpose:: uikit, role::enduser


#### 脚本

标准的 deb 脚本有4个, preinst, postinst, prerm 和 postrm ; 在 Cydia 中还存在一
个独立的脚本, extrainst_ , 从字面上来讲就是额外的安装脚本.

* preinst		在复制文件前执行的脚本
* postinst		在复制文件之后执行的脚本
* prerm			在卸载前执行的脚本
* postrm		在卸载之后执行的脚本
* extrainst_	这个脚本是为解决某些脚本只需要在安装时执行, 在升级时不执行
				而专门引入的一个脚本, 功能跟postinst差不多


### 生成 deb 包

开始打 deb 包, 即会生成 VPlayer.deb
    $ dpkg-deb -b VPlayer

使命名规范, 将会重命名为 com.yixia.VPlayer_0.0.5_iphoneos-arm.deb
    $ dpkg-name VPlayer.deb

至此, deb 包就制作完成了.


## 发布 deb 包

### 发布到已有的仓库

这容易, 只需要注册一个id, 上传deb包就可以了, 可以发布到一些有名的repo:

[BigBoss][re1]  
[modmyi][re2]  
[威锋网][re3]  
[BigBoss-submit](http://thebigboss.org/hosting-repository-cydia/submit-your-app)


### 创建自己的仓库

如果要创建自己的仓库, 推荐使用 [MyRepoSpace.com][re4] , 只要注册就可以拥有一个
独立的repo, 并且可以免费使用, 但仓库大小有限制, 只有50M.


## deb 包的安装和卸载

### 安装过程

* 读取数据库并锁定, 避免同时有两个安装程序在运行
* 读取 control 中的 Package(软件包标识)和版本信息, 并搜索数据库, 若已存在, 则
  卸载之后再安装;
* 检查 Depends, Pre-Depends, Conflicts 和 Replaces, 如果检测到已存在 Conflicts
  中存在的软件, 则报错并终止安装. 如未找到 Pre-Depends 指定的软件, 则报错并终
  止安装. 如找到 Replaces 中指定的软件, 则卸载之;
* 将数据写入 /var/lib/dpkg/status 文件中;
* 执行 preinst 脚本(如果有);
* 解包 data.tar.gz, 将文件放置于相应位置, 并将文件列表写入
  /var/lib/dpkg/info/XXX.list;
* 运行 postinst 和 extrainst_ 脚本(如果有);
* 如果之前的安装都没有出错, 即安装成功, 那么会在 status 文件中写入 Status:
  install ok installed 信息, 否则会写入其它状态数据, 比如 Unpacked (未解包数据
  ),Failed-config(脚本未能成功执行),Half-installed(安装失败等);
* 重新加载数据库并解除锁定.


### 安装方法

* Cydia 或同类 APT 管理软件在线安装, 这个是最佳的安装方式, 因为通常无需考虑依
  赖关系, 但缺点是对网络的要求比较高;
* 命令行中以 dpkg -i XXX.deb 的形式安装, 好处是可以以通配符一次性安装多个 deb,
  而且也可以直接看到脚本的运行状况和安装成功/失败的提示信息, 缺点是需要命令行
  软件的支持, 如 Putty/WinSCP的控制台/iSSH/MobileTerminal, 很多人也不熟悉命令
  行下的操作. 另外, 安装完后会不显示图标;
* 放置于 AutoInstall 目录重启安装. 该方法实际是 Cydia 提供的一个启动脚本, 在每
  次系统启动时以 dpkg 命令安装 /va/root/Media/Cydia/AutoInstall 目录下的 deb,
  好处是不需要命令行操作, 缺点是必须要重启, 有些甚至要重启两次, 也会出现不显示
  图标的情况;
* 利用 iFile 安装, 复制 deb 包到任意目录, 然后在 iFile 中点击选择安装. 好处是
  图形化操作, 桌面会显示图标(需要等几分钟才出现), 缺点是不能一次安装多个deb;
* 用 Cyder II 等软件来安装, 其原理是模拟一个 APT 软件管理器来下载相应的 deb 文
  件并传到设备, 然后以前面几种方式来安装.

总体来说, deb 的安装都是依赖于 dpkg -i 命令来安装, 只不过有些是在命令行下输入
命令, 有些是提供了图形界面. 但除了 Cydia 安装一种方式之外, 其余几种安装方式都
存在一些共有的问题:

* 不会自行搜索依赖关系, 必须手动提供所依赖的 deb;
* Cydia 会读取安装脚本里的一些特殊语句, 比如, 仅在全新安装时执行而不在升级时执
  行, 安装完成后重启 SpringBoard 或设备等等.


### 卸载过程

* 读取数据库并锁定;
* 根据软件标识搜寻数据库;
* 检查是否有软件依赖于待卸载的软件, 如果有则提示, 并中断卸载;
* 执行 prerm 脚本(如果有);
* 读取 /var/lib/dpkg/info/XXX.list 文件, 并删除 list 文件中记录的全部文件和非
  空文件夹;
* 运行 postrm 脚本;
* 如果卸载命令是 dpkg -r, 则保留 status 中的记录并改为 Status: Not installed;
  如果卸载命令是 dpkg -P, 则删除全部数据;
* 如果卸载过程没有错误的话, 重新读取数据库并解除锁定.

由于 deb 安装的软件可能会在运行时在 /var/mobile/Documents 下放置存档文件, 或在
/var/mobile/Library/Preferences 下放置设置文件, 而这些文件并没有记录在 list 文
件里, 所以卸载的时候不会被删除. 所以, 安装的程序所产生的文件需要在 prerm 或
postrm 脚本中删除.


### 卸载方法

* Cydia 里卸载, 优点是卸载过程和提示信息很详细, 全图形界面操作, 同时也提供了重
  新安装的选项;
* 以 dpkg -r Package_ID 命令或 dpkg -P Package_ID 命令来卸载;
* Cydelete 来卸载, 优点是可以直接在桌面上卸载有图标的软件, 但对那些没有图标的
  无能为力.


## Reference

[cydia app upload?][rf1]  
[Convert ipa file into deb file][rf2]  
[从零开始搭建Cydia软件源+制作deb安装包][rf3]  
[Iphone/ipad安装包的三种格式 deb,ipa 和 pxl 解释][rf4]  


[if1]:http://www.finkproject.org/download/
[if2]:http://www.finkproject.org/download/srcdist.php
[if3]:http://bbs.weiphone.com/read-htm-tid-669283.html

[re1]:http://thebigboss.org/hosting-repository-cydia/submission
[re2]:http://modmyi.com/cydia/
[re3]:http://apt.weiphone.com/index.php?action=uploadSoftwareFile
[re4]:https://www.myrepospace.com/

[rf1]:http://www.sinfuliphone.com/showthread.php?p=870066
[rf2]:http://insanelyi.com/topic/5765-convert-ipa-file-into-deb-file/
[rf3]:http://bbs.weiphone.com/read-htm-tid-669283.html
[rf4]:http://fanlb.blogbus.com/logs/80466716.html
