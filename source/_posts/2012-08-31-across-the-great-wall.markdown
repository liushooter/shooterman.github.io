---
layout: post
title: "翻越GFW 走向世界"
date: 2012-08-31 05:02
comments: true
categories: chrome GAE
---

"Across the Great Wall we can reach every corner in the world."

中国不愧为文明古国，早有先见之明，古有长城，今有×××，人们乐此不疲的到墙外透透气儿。
不知何时翻墙也晋升为程序猿的基本功——仅是个人观点。

在ubuntu用GAE翻墙，简单经济实惠。适合我这种不想买VPN又想看外面的人。
我翻墙更多是浏览网页，基本用不到全局代理，我的标配是chrome和它的一个插件Proxy SwitchySharp。

#### **安装chrome**
尽管有Firefox，大爱还是chrome，先下载最新的deb安装包，然后
```
$ sudo dpkg -i  google-chrome-stable_current_i386.deb
  # 提示没有安装所需要的依赖包
$ sudo apt-get -f install 
  # 安装依赖包
$ sudo dpkg -i  google-chrome-stable_current_i386.deb
```
让我感觉最恐怖的是，chrome 关联好账户后，那些配置和书签都在了。My privacy!

#### **注册Google App Engine帐号**

1.用Google账户登录[Google App Engine](http://code.google.com/appengine/)后，
点击Create an Application创建一个应用程序。

2.输入手机号码，接受创建验证码，必须有此过程，否则无法注册成功。

3.输入验证码后就进入了创建应用程序的详细设置界面。
 填写Application Identifier和Application Title以及勾选同意服务条款，点 Save 即完成创建。
  **在这里把Application Identifier称为app_id**。

#### ** SDK &&fetchserver &&localproxy**
1.下载 [Google App Engine SDK](http://googleappengine.googlecode.com/files/google_appengine_1.7.1.zip)，
这是python版的SDK，因为ubuntu自带了python，解压sdk到任意文件夹。

2.在[这里](http://code.google.com/p/gappproxy/downloads/list)，
下载对应的localproxy和fetchserver的linux版本。
**解压fetchserver文件夹至Google App Engine SDK目录**。

3.打开fetchserver文件夹内的app.yaml文件，修改第一行 
```
application: your_application_name
```
your_application_name改为刚才输入的app_id，
再打开localproxy文件夹的proxy.conf文件，修改最后一行
```
#fetch_server = http://your-fetch-server.appspot.com/fetch.py
```
去掉#，your-fetch-serve改为刚才输入的app_id。

 4.上传fetchserver：进入Google App Engine SDK目录，运行命令appcfg.py update fetchserver，会要求你输入Google 账户及密码，之后关闭结束上传。

 5.运行proxy.py。

 进入localproxy文件夹，然后
 ```
 $ python proxy.py
 ```

#### **安装Proxy SwitchySharp插件**
从Chrome 网上应用店安装Proxy SwitchySharp，配置 HTTP Proxy 为 127.0.0.1 ，Port 为8000。
然后保存。被墙的时候，点击Proxy SwitchySharp，刷新页面即可。

#### **快捷方式**
若每次都要翻墙都要用到命令行，一定会吐血。我用到一个简单的方法，就是给文件添加链接到桌面
```
#文件添加执行权限
$ sudo chmod +x proxy.py
# 添加链接
$ ln ~/GFW/localproxy-2.0.0/proxy.py  ~/Desktop/killer
```
只要在桌面双击killer即可。

今天是农历七月十五，有些想妈妈了，愿她一切都好。