---
layout: post
title: "Ubuntu 12.04的安装和配置(一)"
date: 2012-08-26 17:26
comments: true
categories:  ubuntu ruby
---
 [Ubuntu 12.04](http://www.ubuntu.com/) 出来有段时间了，一直没有更新，本想从11.10的系统
 直接升级，结果脆生生挂掉了，好多东西拿不出来了。杯具，重装吧！

 主要是 ruby on rails 和它相关联的在 ubuntu 上的安装配置。
 包括 [rvm](https://rvm.io/) , ruby on rails, Sublime Text, 
 [passenger](http://www.modrails.com/), [nginx](http://wiki.nginx.org/Main), 
 [mongodb](http://www.mongodb.org/), [redis](http://redis.io/) 的安装，
 顺便把 [Node.js](http://nodejs.org/) 也装上，当作汇总了。
## 安装Ubuntu 12-04
首先准备好12.04的 iso 镜像，需要先从官网下载，我是把iso文件写入U盘后安装的。
从10.10以后，用 UltraISO 制作出来的 Ubuntu 启动盘会出现无法启动的现象，有人说 ImageWriter 很靠谱， 
先用了[ImageWriter](https://github.com/downloads/openSUSE/kiwi/ImageWriter.exe)，
不过我的本本提示没有写入权限，所以找到了[另一个版本](https://launchpad.net/win32-image-writer/)，
 顺利写入。然后插上U盘，重启，设置从U盘启动，基本一路 next，结果手欠，装成E文的系统了。

### 安装配置
#### **中文输入** 
12.04 的中文输入是个大问题， 即使能输入中文，在低版本的 Sublime Text 中也不能输入中文，
好在 **dev版本在其他输入法下** 已经ok了。

#### **安装 Ruby On Rails**   

先安装一些 ruby on rails 的依赖包，听前辈们的话不会错。

```
$ sudo apt-get install -y build-essential openssl curl libcurl3-dev libreadline6 libreadline6-dev git zlib1g zlib1g-dev libssl-dev libyaml-dev libxml2-dev libxslt-dev autoconf automake libtool imagemagick libmagickwand-dev libpcre3-dev libsqlite3-dev
```
**No.1 安装 RVM**  
```
 $ curl -L get.rvm.io | bash -s stable 
```
 给 ~/.bashrc (Ubuntu) 加上脚本引用 

```
  $ echo '[[ -s " HOME/.rvm/scripts/rvm" ]] && source " HOME/.rvm/scripts/rvm"' >> ~/.bashrc
```
 然后，重新载入那个文件

```
 $ source ~/.bashrc 
```
测试一下

```
$ rvm -v
rvm 1.15.5 (stable) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]
```
**No.2  用 RVM 安装 Ruby 环境**

```
$ rvm list known #显示可供选择的版本

[ruby-]1.8.6[-p420]
[ruby-]1.8.7[-p370]
[ruby-]1.9.1[-p431]
[ruby-]1.9.2-p180
[ruby-]1.9.2-p290
[ruby-]1.9.2-p318
[ruby-]1.9.2[-p320]
[ruby-]1.9.2-head
[ruby-]1.9.3-preview1
[ruby-]1.9.3-rc1
[ruby-]1.9.3-p0
[ruby-]1.9.3-p125
[ruby-]1.9.3[-p194]
[ruby-]1.9.3-head
ruby-head
......

# 要安装1.9.3，直接
$ rvm install 1.9.3  
```

.................. 漫长的的等待 ..................

Ruby 装好以后，通过 RVM 将指定版本的 Ruby 设置为系统默认版本

``` 
$ rvm  1.9.3 --default 
```
测试一下

``` 
$ ruby -v
ruby 1.9.3p194 (2012-04-20 revision 35410) [i686-linux]
$ gem -v
1.8.23
```
**No.3 安装 Rails 环境**

```
$ gem install bundler rails
```
测试一下

```
$ bundle -v
Bundler version 1.1.5
$ rails -v
Rails 3.2.8
```
 ruby on rails 的安装齐活了。

#### **安装 Sublime Text** 
由于**输入汉字**原因，安装 Sublime Text 的dev版本，就是比较新的。
先 google 一下 Sublime Text 的ppa源，好吧，ppa暂且是什么先不管。

```  
$ sudo add-apt-repository ppa:webupd8team/sublime-text-2
$ sudo apt-get update # 更新软件源
$ sudo apt-get install sublime-text-dev
```
