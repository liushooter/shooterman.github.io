---
layout: post
title: "rails 页面最佳实践"
date: 2014-01-15 22:18:18 +0800
comments: true
categories: rails best_practices
---

## 什么是好的 view

### 为什么需要最佳实践？

* 项目太大，难懂
* 团队编程风格不同

### 你的 View 可能正面临的问题

* Complex UI with logic UI 和代码逻辑纠缠在一起
* Too long to maintain 代码太长难以维护
* Low performance 性能不好
* Security issues 容易产生安全问题


### 什么是好的代码

* Readability 易读，容易了解
* Flexibility 弹性，容易扩充
* Effective 效率，写码快速
* Maintainability 维护性，容易找到问题
* Consistency 一致性，遵循管理无须死背
* Testability 可测行，容易编写测试


## 18招技巧

### No.1 Move logic to helper

BAD:

```erb
<% if current_user && current_user == post.user %>
  <%= link_to("Edit", edit_post_path(post))%>
<% end %>
```

GOOD:

```erb
<% if editable?(post) %>
  <%= link_to("Edit", edit_post_path(post))%>
<% end %>
```

### No.2 Pre-decorate with Helper

经常用的地方先用 helper 整理

BAD:

```erb
<%= @topic.content %>
```

```erb
<%= auto_link(truncate(simple_format(topic.content)), :length => 100) %>
```

GOOD:

```erb
<%= render_post_content(@topic.content) %>
```

Common Case

* render_post_author
* render_post_published_date
* render_post_title
* render_post_content

### No.3 Use Ruby in Helper ALL THE TIME

在 Helper 中只使用 Ruby 代码，而不要混杂 HTML。因为如果在 Helper 中最后调用 **raw** 或者 **html_safe** 会让代码有被 javascript 攻击的危险

BAD:

```ruby
def render_post_taglist(post, opts = {})
  # ....
   raw tags.collect { |tag|  "<a href=\"#{posts_path(:tag => tag)}\" class=\"tag\">#{tag}</a>" }.join(", ")
end
```

GOOD:

```ruby
def render_post_taglist(post, opts = {})
# ...
  raw tags.collect { |tag| link_to(tag,posts_path(:tag => tag)) }.join(", ")
end
```

### No.4 mix Helper & Partial

在 Helper 中可以混合使用 Partial

BAD:

```ruby
def render_post_title(post)
  str = ""
  str += "<li>"
  str += link_to(post.title, post_path(post))
  str += "</li>"
  return raw(str)
end
```

GOOD:

```ruby
def render_post_title(post)
  render :partial => "posts/title_for_helper", :locals =>
{ :title => post.title }
end
```

### No.5 Tell, Don’t ask

先 Query 再传入 Helper。这样可以避免效能低下的问题。

BAD:

```ruby
def render_post_taglist(post, opts = {})
  tags = post.tags
  tags.collect { |tag| link_to(tag, posts_path(tag: tag)) }.join(", ")
end
```

```erb
<% @posts.each do |post| %>
  <%= render_post_taglist(post) %>
<% end %>
```

GOOD:

```ruby
def render_post_taglist(tags, opts = {})
  tags.collect { |tag| link_to(tag, posts_path(tag: tag)) }.join(", ")
end
```

```erb
<% @posts.each do |post| %>
    <%= render_post_taglist(post.tags) %>
<% end %>

def index
  @posts = Post.recent.includes(:tags)
end
```

### No.6 Wrap into a method

属性尽量包装成 method 而不是放在 helper 中

BAD:

```ruby
def render_comment_author(comment)
  if comment.user.present?
    comment.user.name
  else
    comment.custom_name
  end
end
```

GOOD:

```ruby
def render_comment_author(comment)
  comment.author_name
end

class Comment < ActiveRecord::Base
  def author_name
    if user.present?
      user.name
    else
      custom_name
    end
  end
end
```

### No.7 Move code to Partial

view code 超过两页请注意

* highly duplicated 内容高度重复
* independent blocks 可以独立作为功能区块

Common Case

* nav/user_info
* nav/admin_menu
* vendor_js/google_analytics
* vendor_js/disqus_js
* global/footer

### No.8 Use presenter to clean the view

使⽤ Presenter 解決 login in view 问题

BAD:

```erb
<%= if user_rofile.has_experience? && user_rofile.experience_public? %>
  <p><strong>Experience:</strong> <%= user_profile.experience %></p>
<% end %>
```

GOOD:

```erb
<% user_profile.with_experience do %>
  <p><strong>Experience:</strong> <%= user_profile.experience %></p>
<% end %>
<% user_profile.with_hobbies do %>
  <p><strong>Hobbies:<strong> <%= user_profile.hobbies %></p>
<% end %>
```

```ruby
class ProfilePresenter < ::Presenter
  def with_experience(&block)
    if profile.has_experience? && profile.experience_public?
      block.call(view)
    end
  end
end
```

**关于 presenter 的延伸阅读**

* [Exhibit vs Presenter](http://mikepackdev.com/blog_posts/31-exhibit-vs-presenter)
* [Using Presenters in rails](http://stackoverflow.com/questions/15212637/using-presenters-in-rails)
* [Presenters from Scratch](http://railscasts.com/episodes/287-presenters-from-scratch)
* [Rails: Presenter Pattern](http://blog.jayfields.com/2007/03/rails-presenter-pattern.html)
* [Rails Presenters – Skinny Everything](http://new-bamboo.co.uk/blog/2013/04/17/rails-presenters-skinny-everything)


### No.9 Cache Digest

过去的页面缓存，我们需要自己管理版本，很难维护。新的 digest 缓存策略会自动失效。

BAD:

```erb
<% cache [v15,@project] do %>
  aaa
  <% cache [v10,@todo] do %>
    bbb
    <% cache [v45,@todolist] do %>
      zzz
    <% end %>
  <% end %>
<% end % %>
```

GOOD:

```erb
<% cache @project do %>
  aaa
  <% cache @todo do %>
    bbb
    <% cache @todolist do %>
      ccc
    <% end %>
  <% end %>
<% end %>
```

**关于 Cache Digest 的延伸阅读**

* [cache_digests on github](https://github.com/rails/cache_digests)
* [Cache Digests](http://railscasts.com/episodes/387-cache-digests)


### No.10 Cells

当同一个页面含有多个 Tab ，它会造成如下问题：

* Controller code 特别长，因为需要取出很多不同数据存入的变量
* 在 controller & view 有性能问题不好找到
* 很难设置 action 级别的缓存

BAD:

```ruby
class UsersController < ApplicationController
  def show
    @user = User.find(params[:id])
    @recent_posts = @user.recent_posts.limit(5)
    @favorite_posts = @user.favorite_posts.limit(5)
    @recent_comments = @user.comments.limit(5)
  end
end
```

```erb
<%= render :partial => "users/recent_post", :collection => @recent_posts %>
<%= render :partial => "users/favorite_post", :collection => @favorite_posts %>
<%= render :partial => "users/recent_comment", :collection => @recent_comments %>
```

GOOD:

```erb
<%= render_cell :user, :rencent_posts, :user => @user %>
<%= render_cell :user, :favorite_posts, :user => @user %>
<%= render_cell :user, :recent_comments, :user => @user %>
```

```ruby
class UsersController < ApplicationController
  def show
    @user = User.find(params[:id])
  end
end
```

```ruby
class UserCell < Cell::Rails
  cache :recent_posts, :expires_in => 1.hours
  cache :favorite_posts, :expires_in => 3.hours
  cache :recent_comments, :expires_in => 5.hours

  def recent_posts(args)
   #...
  end

  def favorite_posts(args)
    #...
  end

  def recent_comments(args)
    #...
  end
end
```

**关于 Cells 的延伸阅读**

* [cells on github](https://github.com/apotonick/cells)

### No.11 content_for / yield

根据 Yahoo 大神的建议，应该把 javascript 和 css 代码放在页面的最下面。以便加快加载，但是会造成一些问题。比如你自定义的 js 代码比 jquery 库更早加载，以至于无法运行你的代码。

例子1:


```erb
<!-- application.html.erb -->
<%= stylesheet_link_tag "application" %>
<%= yield %>
<%= javascript_include_tag "application" %>
<%= yield :page_specific_javascript %>

<!-- main.html.erb -->
<%= content_for :page_specific_javascript do %>
  <script type= "text/javascript">
    // your script here
  </script>
<% end %>
```

例子2:

```erb
<!-- application.html.erb -->
<div class="main">
  <%= yield %>
</div>
<div class="sidebar">
  <%= yield :sidebar %>
</div>

<!-- main.html.erb -->
<%= content_for :sidebar do %>
  <%= render "ad/foo"%>
<% end %>
```

### No.12 Decoration in Controller

有个别情况，可以把一些代码放在 controller 中

BAD:

```erb
<%= content_for :meta do %>
  <meta content="xdite's blog" name="description">
  <meta content="Blog.XDite.net" property="og:title">
<% end %>
```

GOOD:

```ruby
def show
  @blog = current_blog
  drop_blog_title @blog.name
  drop_blog_descption @blog.description
end
```

```erb
<%= stylesheet_tag "application" %>
<%= render_page_title %>
<%= render_page_descrption %>
```

### No.13 Decoration using I18n

善用 I18n 也可以帮你减少一部分 View 代码


BAD:

```ruby
def render_user_geneder(user)
  if user.gender == "male"
    "男 (Male)"
  else
    "⼥女 (Female)"
  end
end
```

GOOD:

```ruby
def render_user_gender(user)
  I18n.t("users.gender_desc.#{user.geneder}")
end
```

### No.14 Decoration using Decorator

不要把所有代码都往 Model 中扔，下面的代码演示了一种情况，你需要格式化一种显示发布时间的方法


BAD:

```ruby
# first helper
def render_article_publish_status(article)
  if article.published?
    "Published at #{article.published_at.strftime('%A, %B %e')}"
  else
    "Unpublished"
  end
end

# then model method

class Article < ActiveRecord::Base
  def human_publish_status
    if published?
      "Published at #{article.published_at.strftime('%A, %B %e')}"
    else
      "Unpublished"
    end
  end
end

# then ...

class Article < ActiveRecord::Base
  def human_publish_status
  end

  def human_publish_time
  end

  def human_author_name
  end
  #........
end

# at last

class Article < ActiveRecord::Base
  include HumanArticleAttributes
end
```

下面的代码演示使用 https://github.com/drapergem/draper 之后

GOOD:

```erb
<%= @article.publication_status %>
```

```ruby
class ArticleDecorator < Draper::Decorator
  delegate_all
  def publication_status
    if published?
      "Published at #{published_at}"
    else
      "Unpublished"
    end
  end

  def published_at
    object.published_at.strftime("%A, %B %e")
  end
end

def show
  @article = Article.find(params[:id]).decorate
end
```

**关于 draper 的延伸阅读**

* [draper on github](https://github.com/drapergem/draper)
* [rails_presenters gems](https://www.ruby-toolbox.com/categories/rails_presenters)

### No.15 Decoration using View Object

有时候，我们希望在用户中，不列出我自己


BAD:

```erb
<dl class="event-detail">
  <dt>Event Host</dt>
  <dd>
    <% if @event.host == current_user %>
      You
    <% else %>
      <%= @event.host.name %>
    <% end %>
  </dd>
  <dt>Participants</dt>
  <dd>
    <%= @event.participants.reject { |p| p == current_user }.map(&:name).join(", ") %>
  </dd>
</dl>
```

GOOD:

```ruby
class EventDetailView
  def initialize(template, event, current_user)
    @template = template
    @event = event
    @current_user = current_user
  end
  def host
    if @event.host == @current_user
      "You"
    else
      @event.host.name
    end
  end
  def participant_names
    participants.map(&:name).join(", ")
  end
  private

  def participants
    @event.participants.reject { |p| p == @current_user }
  end
end
```

```erb
<dl class="event-detail">
  <dt>Host</dt>
  <dd><%= event_detail.host %></dd>
  <dt>Participants</dt>
  <dd><%= event_detail.participant_names %></dd>
</dl>
```

### No.16 Form builder

* simple_form
* bootstrap_form

### No.17 Form Object

在 FORM 中包装逻辑，而不是 model 或 controller。比如我们经常遇到的一个情况，只有同意某某条款才能继续注册表单。

**Reform** Decouples your models from form validation, presentation and workflows.

https://github.com/apotonick/reform

### No.18 Policy Object / Rule Engine

权限控制

BAD:

```ruby
def render_post_edit_option(post)
  if post.user == current_user || current_user.admin?
    render :partial => "post/edit_bar"
  end
end
```

针对于这种情况建议使用权限控制系统，另外近来似乎大家都从 Cancan 转移到 Pundit 去了

**Pundit** Minimal authorization through OO design and pure Ruby classes

https://github.com/elabs/pundit

**Cancan** Authorization Gem for Ruby on Rails

https://github.com/ryanb/cancan

## 总结

* 总是假设这块正在实现的功能代码需要 decorated（修饰）
* 把逻辑移到 methods/classes
* 避免在 View/Helper 中做数据库查询
* 当代码很难懂的时候，把它抽取到一个新的控制中心比如：form object, Policy Object 之类的

## 延伸阅读

* http://blog.xdite.net
* https://github.com/bloudermilk/maintainable_templates http://pivotallabs.com/form-backing-objects-for-fun-and-profit/
* http://saturnflyer.com/blog/jim/2013/10/21/how-to-make-your-code-imply-responsibilities/
* http://objectsonrails.com/

## 出处

以上内容由 **Victor** 整理，本人只是记录在blog中。