---
layout: post
title: "ruby的yield和闭包"
date: 2012-09-15 02:06
comments: true
categories:  ruby rails
---
**First Blood**  来些血腥凶残暴力的！
这些经常在面试中被提问，内容很多。
从面试里单独拿出来，这东西相当于神器级别，必须谨慎对待。

yield是ruby中一个特殊的关键字，可以调用块方法(block)，而block是ruby闭包的一种，
故将三者联系起来，yeild天生就是闭包的小跟班儿。感觉没必要单独讲，不然很割裂的赶脚。

### ** ruby 闭包**   block，Proc，lambda

__block 与 yield__
block 是最常用到的，有两种形式,{}和do ... end。
一般单行习惯用{}，多行用do ... end，当然这只是习惯。

``` ruby demo.rb
def test_yield
  yield
end  
     
test_yield{ puts "this is first block." } # yield可以调用block 即{...}

test_yield do
  puts "this is second block." 
end

def test2_yield  
  yield("shooter")  
end  

test2_yield{ |var| puts "This args is #{var}." } 
#传给yield的参数(此处为shooter)即对应了block中的参数(||中的部分)
```

还可以通过 Kernel#block_given? 方法询问当前的方法调用是否包含块。
```ruby
def test
  return yield if block_given?
  puts 'no block'
end

test # => no block
test{ puts "Here's a block" } # => Here's a block
```

__Proc对象__

```ruby
plus = Proc.new { |x| x + 1 }
puts plus.call(2) # =>3

subtract = proc { |x| x - 1 }
puts subtract.call(4) # =>3
``` 
这被称为**延迟执行**(Defferred Evaluation)。

通过**&操作符**，block 与 Proc对象可以相互转化。

```
def math_one(a, b, &oper)
  puts yield(a,b) 
end

def math_two(a, b, &oper)
  puts oper.call(a,b)
end

math_one(2,3) { |x,y| x + y} # => 2 + 3 = 5
math_two(2,3) { |x,y| x * y} # => 2 * 3 = 6
```
那么这个&起了什么作用？
```ruby
def my_method(&code)
  code
end

p = my_method{ |name| "Hello,#{name}!"}
puts p.class # => Proc
puts p.call("shooter") # => Hello,shooter!
```
&操作符的真正含义：Proc对象和块之间的切换符号。&code是一个块，code是一个Proc对象。
简单的去掉&操作符，我们就能再次得到一个Proc对象。
```
def proc_to_block(greeting)
  puts "#{greeting},#{yield}"
end

my_proc = Proc.new {"shooter!"}
puts my_proc.class
proc_to_block("Hello", &my_proc)
```
__lambda函数__
lambda跟Proc就像两个双胞胎
```
plus = lambda { |x| x + 1 }
puts plus.call(2) # =>3
```
lambda跟Proc有两个最明显的区别: <br/>
1. lambda检查参数的个数，Proc不会。<br/>
&nbsp;在Proc中，多余的参数被设为nil。但lambda中，Ruby会抛出一个ArgumentError错误。<br/>
2. return不同。<br/>
&nbsp;lambda的return是返回值给方法，方法会继续执行。Proc的return会终止方法并返回得到的值。 
```
def proc_return
  Proc.new { return "Proc.new"}.call
  return "proc_return method finished"
end

def lambda_return
  lambda { return "lambda" }.call
  return "lambda_return method finished"
end

puts proc_return    # => Proc.new
puts lambda_return # => lambda_return method finished
```
看了代码会一目了然。参考了不少资料，就是拼凑的起来的。