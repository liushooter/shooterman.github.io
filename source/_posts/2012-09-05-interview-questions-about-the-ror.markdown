---
layout: post
title: "RoR的一些面试经验(一)"
date: 2012-09-05 01:30
comments: true
categories: ruby rails
---

 那些天我不是在面试，就是在面试的路上，搞得很懈怠，主要还是精神上的。
 有的中规中矩;有的问题很琐碎;有的很BT,概念性的东西一股闹的全抛出来了......
 我记性不太好，对概念不感冒，倒不是从小这样，记得当年还背过视力表，
 可能三聚氰胺喝多了吧。 

### Symbol与String  
在rails中经常看到Symbol，像 ```:name  :email params[:user]```，
这种已:开头的变量就是Symbol对象。貌似我经常把两者混用，还有些不同的。

**创建 symbol 对象**
```
 :foo
 :"shooter"
```

在 Ruby 中每一个对象都有唯一的对象标识符（Object Identifier），可以通过 object_id 方法来得到一个对象的标识符。

```
1.9.3p194 :001 > 3.times{ puts :foo.object_id }
230808
230808
230808
 => 3 
1.9.3p194 :002 > 3.times{ puts "foo".object_id }
85823400
85823380
85823350
 => 3 
```
一个字符串每出现一次 Ruby 就会创建一个 String对象，即便他们包含了相同的字符串内容；
而对于 Symbol 对象，一个名字（字符串内容）唯一确定一个 Symbol 对象。
Symbol 对象一旦定义将一直存在，你不能对symbol赋值，
而且在运行环境中symbol的值也不会改变，直到程序执行退出。

**相互转化**

``` 
# Symbol到String

1.9.3p194 :001 > :foo.id2name
 => "foo" 
1.9.3p194 :002 > :foo.to_s
 => "foo" 
1.9.3p194 :003 > :"I am a rubyist"
 => :"I am a rubyist" 

# String到Symbol

1.9.3p194 :004 > "rubyist".to_sym
 => :rubyist 
1.9.3p194 :005 > "rubyist".intern
 => :rubyist 
```

从实用角度看，在一些场景下Symbol好过String只有两点：**节省内存，表达简洁** <br/>
通常来讲，当你面临 String 还是 Symbol 的选择时，可以参考以下标准：<br/>
&nbsp;&nbsp;如果使用字符串的内容，这个内容可能会变化，使用 String <br/>
&nbsp;&nbsp;如果使用固定的名字或者说是标识符，使用 Symbol。

这是第一篇，没想到一个内容就这么多，再写东西就有些多了，果断阉割了。