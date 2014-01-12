---
layout: post
title: "我用到的 Sublime Text 2 插件"
date: 2012-08-08 20:24
comments: true
categories:  sublime
---

 [Sublime Text 2](http://www.sublimetext.com/) 是在俺剛學習**Ruby**時大大們極力推薦的，真的超贊。
 Sublime Text是用[python语言](http://zh.wikipedia.org/zh/Python)编写。如果熟悉[python](http://www.python.org/)，你也可以编写插件實現自己想要的功能。目前插件集中在github上，需要在线安装。安装插件前需要 **安装包控制(Package Control)** 

### 安装包控制(Package Control)
 * 打开Sublime Text 2，按下  ``` Ctrl+` ``` 调出 Console。
 * 将以下代码粘贴进命令行中并回车:
```  
import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())
```
* 重启 Sublime Text 2，如果在 ``` Preferences -> Package Settings ```中见到
   ``` Package Control ```这一项，就说明安装成功了。

### 使用 Package Control 安装插件
* 按下``` Ctrl+Shift+P ```，调出窗口

* 在输入框中输入``` install ```，将智能模糊提示，光标选中 ``` Package Control: Install Package```，果断回车

* 在新弹出的窗口中，(假设SublimeREPL)，直接将插件名(如 SublimeREPL)复制到输入框中

* 大功告成，有时特殊情况，需要在第二步前，先通过输入repository 添加插件的url，重复第二步，然后真正的ok。
 **MarkdownEditing** 是这样滴情况。
 

### 我的插件集

俗话就说嘛 好记性不如烂笔头 ^_^

* [Gist](https://github.com/condemil/Gist)  gist代码分享
 
* [SublimeREPL](https://github.com/wuub/SublimeREPL)
 将 Ruby Shell (即 IRB)整合到 Sublime Text 中,而且集成了 node coffeescript lua 等，不过需要配置好环境，
 偶的只有irb，其他都是提示错误。

* [MarkdownEditing](http://ttscoff.github.com/MarkdownEditing/)   markdown编辑插件

* [Markdown Preview](https://github.com/revolunet/sublimetext-markdown-preview) markdown预览插件

感觉每个插件都大有搞头的样子 ，以后再说吧。

PS: 第一次写东西，刚刚上手[markdown](http://markdown.tw/)，参考了网上n多资料，还要亲手尝试一遍，恨费工夫和精力，好在没有神马意外情况。
