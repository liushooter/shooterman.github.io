---
layout: post
title: "Ubuntu 12.04的安装和配置(二)"
date: 2012-08-27 20:12
comments: true
categories:  ubuntu ruby
---
上一篇坑挖了一半，今天一定要把坑挖好。

继续 [passenger](http://www.modrails.com/), [nginx](http://wiki.nginx.org/Main), 
 [mongodb](http://www.mongodb.org/), [redis](http://redis.io/), 
 [Node.js](http://nodejs.org/)  的安装。

#### **安装Node.js**
配置好ruby on rails 环境后，创建一个rails 项目

```
$ rails new demo
 ……
$ cd demo
$ rails s
```
一般会提示这样的错误，```Could not find a JavaScript runtime ...```，
具体请移步 [stackoverflow](http://stackoverflow.com/questions/8059332/could-not-find-a-javascript-runtime-see-https-github-com-sstephenson-execjs-f)
 
在ubuntu环境下，安装node.js即可

```
$ sudo apt-get install nodejs
```
完成后再次运行项目 

#### **安装passenger和nginx**

安装passenger

```
$ gem install passenger
``` 
安装passenger的nginx模块，nginx一并安装，需要root，我们可以用rvmsudo

``` 
rvmsudo passenger-install-nginx-module
# 提示哪种安装模式

1. Yes: download, compile and install Nginx for me. (recommended)
 # 完全安装
 
2. No: I want to customize my Nginx installation. (for advanced users)
 # 自定义安装
```
选择1，回车，进一步提示安装位置，直接回车，nginx 将安装在默认位置，即/opt/nginx。
添加nginx启动脚本，参考[这里](http://wiki.nginx.org/Nginx-init-ubuntu)。

```
$ sudo mv nginx /etc/init.d   # 脚本移动到 /etc/init.d
$ sudo chmod +x nginx         # 添加执行权限

$ sudo /etc/init.d/nginx start
# * Starting Nginx Server...                   [ OK ]

$ sudo /etc/init.d/nginx restart
# * Stopping Nginx Server...                   [ OK ]
# * Starting Nginx Server...                   [ OK ]

$ sudo /etc/init.d/nginx stop
# * Stopping Nginx Server...                   [ OK ]
```

**nginx的配置**

```
$ sudo vim /opt/nginx/conf/nginx.conf              # 打开配置文件
......
http {
    passenger_root ～/.rvm/gems/ruby-1.9.3-p194/gems/passenger-3.0.15;
    passenger_ruby ~/.rvm/wrappers/ruby-1.9.3-p194/ruby;

    include       mime.types;
    default_type  application/octet-stream;
     ......
    server {
        listen 80;                           #端口
        server_name  localhost;              #域名
        root ~/www/demo/public;  #项目public路径
        passenger_enabled on;
}
```

这样就可以访问 http://localhost 了

#### **安装 mongodb**

```
$ sudo apt-get install mongodb
```
运行

```
$ mongo
MongoDB shell version: 2.0.4
connecting to: test
```

#### **安装redis**
下载并安装redis
```
wget http://redis.googlecode.com/files/redis-2.4.16.tar.gz
tar -zxf redis-2.4.16.tar.gz
cd redis-2.4.16 
make 
sudo make install
```
#### **安装mysql**

```
$ sudo apt-get install mysql-server mysql-client
```
全都大功告成 ^_^

PS 发现网页被墙了,ubuntu 也跑不了翻墙，Fuck GFW!