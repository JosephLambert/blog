# Ruby 豆知识



## 中文编码

test.rb

```ruby
#!/usr/bin/ruby -w
# -*- coding: UTF-8 -*-

puts "你好，世界！"
```



## Here Document

多行字符串

```ruby
#!/usr/bin/ruby -w
# -*- coding : utf-8 -*-

print <<EOF
    这是第一种方式创建here document 。
    多行字符串。
EOF

print <<"EOF";                # 与上面相同
    这是第二种方式创建here document 。
    多行字符串。
EOF

print <<`EOC`                 # 执行命令
	echo hi there
	echo lo there
EOC

print <<"foo", <<"bar"	      # 您可以把它们进行堆叠
	I said foo.
foo
	I said bar.
bar
```



## BEGIN/END 块

```ruby
#!/usr/bin/ruby

puts "This is main Ruby Program"

# 在程序结尾调用
END {
   puts "Terminating Ruby Program"
}

#在程序开始调用
BEGIN {
   puts "Initializing Ruby Program"
}
```



## 注释

```ruby
# 行注释

=begin
块注释
这是注释。
这也是注释。
这也是注释。
这还是注释。
=end
```



## 变量

- 局部变量
- 实例变量
- 类变量
- 全局变量
- 常量
- 伪变量：有着局部变量的外观，但行为却像常量。
  - **self:** 当前方法的接收器对象。
  - **true:** 代表 true 的值。
  - **false:** 代表 false 的值。
  - **nil:** 代表 undefined 的值。
  - **\__FILE__:** 当前源文件的名称。
  - **\__LINE__:** 当前行在源文件中的编号。



## 方法的参数

```ruby
def method_name (var1, var2)
   expr..
end

# 参数设置默认值
def method_name (var1=value1, var2=value2)
   expr..
end

# 参数设置默认值+自由顺序
def method_name(x:0, y:0, z:0)
	expr..
end
p method_name(x: 2, y: 3, z: 4)
p method_name(z: 4, y: 3, x: 2)

# 可变数量的参数
def sample (*var_ary)
   puts "The number of parameters is #{var_ary.length}"
   for i in 0...var_ary.length
      puts "The parameters are #{var_ary[i]}"
   end
end

```



## 带块方法

方式一

```ruby
def test
   yield 5
   puts "在 test 方法内"
   yield 100
end
test {|i| puts "你在块 #{i} 内"}
```

方式二

```ruby
def test(&block)
   block.call(1)
end
test {|i| puts "你在块 #{i} 内"}
```



## 多线程

**创建线程**

Thread.new 



**线程编程惯例**

1. 在 t = Thread.new {...} 和 t.join 之间, 通常会存在 `同样费时` 的逻辑代码.
2. 因此, 多数情况下, t.join , 写在调用代码的最后一行.

只要满足这两点, Thread 代码块中的代码, 就可以和主线程中的代码 `同时执行`(具体是并发还是真并行, 依赖于平台), 并且, 主线程如果先完成, 还会等待子线程也完成后, 才会退出。



**线程中的异常**

- 当某线程发生异常，且没有被rescue捕捉到时，该线程通常会被无警告地终止。
- 若有其它线程因为Thread#join的关系一直等待该线程的话，则等待的线程同样会被引发相同的异常。
- 手动设置使得发生异常时中断解释器 t.abort_on_exception = true



**线程同步**

在Ruby中，提供三种实现同步（在多个线程钟同时需要一个程序变量）的方式，分别是：

1. 通过Mutex类实现线程同步

2. 监管数据交接的Queue类实现线程同步

3. 使用ConditionVariable实现同步控制

[参考链接](https://blog.csdn.net/watkinsong/article/details/7797509)



**线程互斥**

用于多线程编程中，防止两条线程同时对同一公共资源（比如全局变量）进行读写的机制。

[参考链接](https://www.cnblogs.com/liupl/p/4506125.html)



**线程变量**

当线程的局部变量需要被别的线程或者主线程访问时，可以把线程对象当做哈希写入或读取。



**线程优先级**

 Thread.priority 读取

 Thread.priority = 写入

线程的优先级默认为 0 。 优先级较高的执行的要快。



## 常用方法拾遗

**判断是否相等**

**.eql?**：如果接收器和参数具有相同的类型和相等的值，则返回 true。1 == 1.0 返回 true，但是 1**.eql?**(1.0) 返回 false。

**equal?**：如果接收器和参数具有相同的对象 id，则返回 true。如果 aObj 是 bObj 的副本，那么 aObj == bObj 返回 true，a**.equal?**bObj 返回 false，但是 a**.equal?**aObj 返回 true。



**判断表达式是否已经定义**

```ruby
defined? variable # 如果 variable 已经初始化，则为 True
defined? method_call # 如果方法已经定义，则为 True
defined? yield   # 如果已传递代码块，则为 True
defined? super     # => "super"（如果可被调用）
```



**定义别名**

alias foo bar：为 bar 定义了别名为 foo



**取消方法定义**

undef bar：取消名为 *bar*的方法定义



**输入输出**

- 与命令行交互：puts 以及 gets
- 文件的读写：I/O 类 以及 File 类