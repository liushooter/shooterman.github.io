---
layout: post
title: "rails使用队列resque"
date: 2013-11-08 12:13:40 +0800
comments: true
categories: rails resque
---


### 准备

[Resque](https://github.com/resque/resque) 依赖 [Redis](http://redis.io/)，
请先安装reids数据库。

***

### 安装Resque
    gem "resque", "~> 1.25.1"

运行 resque-web 会打开 http://0.0.0.0:5678/overview

##### 创建Rake
lib/tasks/resque.rake

```ruby
  require 'resque/tasks'

  task "resque:setup" => :environment

  # task "resque:setup" => :environment do
  #   ENV['QUEUE'] = "*"
  # end
  # 执行 rake resque:work 会启动所有队列

```

##### 创建任务

app/workers/resque_job.rb

```ruby
class ResqueJob
  @queue = :resque_job #定义队列名称

  def self.perform(u_name) #必须定义 执行方法体
    puts "hello #{u_name}"
  end
end

```

##### 启动 queue

rake resque:work QUEUE='*' #启动所有队列

rake resque:work QUEUE='resque_job' #启动队列名称为resque_job的单个队列

##### 调用 queue

```ruby
Resque.enqueue(ResqueJob,"everyone") #调用队列
```

#### 项目中查看resque任务

```ruby
gem "resque", "~> 1.25.1", :require => "resque/server"
mount Resque::Server, :at => "/resque" # routes.rb
```

访问 http://0.0.0.0:3000/resque/overview

#### 限制访问

```ruby
  # config/initializers/resque_auth.rb
  Resque::Server.use(Rack::Auth::Basic) do |user, password|
    password == "password"
  end
```
***

### Redis 命令行

```
redis-server #Redis服务器的daemon启动程序

redis-cli #Redis命令行操作工具。当然，你也可以用telnet根据其纯文本协议来操作

redis-benchmark #Redis性能测试工具，测试Redis在你的系统及你的配置下的读写性能

redis-stat #Redis状态检测工具，可以检测Redis当前状态参数及延迟状况

```
***

### 定时队列

	gem "resque-scheduler", "~> 2.0.1"  :require => "resque_scheduler"
