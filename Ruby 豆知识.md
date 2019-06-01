# Ruby 豆知识



### 中文编码

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

