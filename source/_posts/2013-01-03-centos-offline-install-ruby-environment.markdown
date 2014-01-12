---
layout: post
title: "CentOS 5.6 离线安装Ruby环境及配置项目"
date: 2013-01-03 15:43
comments: true
categories:  ruby  centOS
---
遇到了这么个小问题，需要把完成的railsApp打包到CentOS上，而且服务器不能联网，
ruby 环境也没有安装。一番捣鼓，步骤如下:

**没有找到离线安装rvm的方法 而且 linux版本为CentOS 5.6**

###下载源文件
  
[ruby-1.9.3-p327](http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p327.tar.gz)
 
[yaml-0.1.4.tar.gz](http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz)
 
[bundler-1.2.3](http://rubygems.org/downloads/bundler-1.2.3.gem)
 
### 本地服务器
#### 打包 rails app
```
$ bundle package
#若提示没有,安装bundler并重复以上步骤
$ gem install bundler
$ bundle package # 将项目所需要的gem复制到 vendor/cache
$ tar -czf   railsApp.tar.gz   railsApp #压缩打包
``` 
### 远程服务器
#### **首先安装 yaml-0.1.4.tar.gz，再安装ruby**
```
$ tar –xzvf  yaml-0.1.4.tar.gz
$ ./configure
$ make
$ make install
```

#### 安装ruby
```
$ tar  -xzvf  ruby-1.9.3-p327.tar.gz
$ cd ruby-1.9.3-p327
$ ./configure
$ make
$ make install
$ ruby -v   #提示即安装成功
$ gem -v    #gem已经安装
```

#### 本地安装bundler
```
$ gem install  bundler-1.2.3.gem
$ bundle -v  #提示即安装成功
```

#### 部署 rails app
```
$ tar  -xzvf  railsApp.tar.gz
$ cd railsApp
$ bundle install --local  #本地安装gem
```

**注意修改配置文件**
 
    $ rails s  #测试运行
 
 [分享在gist上](https://gist.github.com/4274057)