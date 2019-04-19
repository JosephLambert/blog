# Ruby元编程

## Ruby元编程介绍和使用场景

* 元编程可以用作DSL，高度抽象之后暴露出来的接口是简单直观的。例如：Rspec
* 因为过于灵活以及技巧高超，有利有弊（团队协作问题以及不易调试）。

## Ruby类结构

* 自定义类、内置类甚至Class类本身都是Class类的实例
* 自定义模块的类是Module，Module的类是Class
* 模块和类的区别只是模块不能实例化,module很大程度上的作用是组织代码
* 注入模块三种方式：include extent prepend
* super调用继承链上层模块或父类的同名方法
* ancestors类方法返回继承链，也就是方法查找链

## Singleton Method单例方法

**定义类方法的方式**

```ruby
# 定义单例方法的方式
# 1 单个
class A
  def self.hi
    p 'hi'
  end
end

# 2 批量
class A
  class << self
    def hello
      p 'hello'
    end
  end
end

# 3 针对实例
def A.hey
  p 'hey'
end

# 4 返回单例作用域
(class << A; self; end).class_eval do
  def you
    p 'you'
  end
end

# 5 返回单例作用域
A.singleton_class.class_eval do
  def folk
    p 'folk'
  end
end

A.hi
A.hello
A.hey
A.you
A.folk
p A.singleton_methods #查看单例方法
```

**self**

```ruby
class A
  p self # => A

  class << self
    p self # => #<Class:A>
  end

  def hello
    p self # => #<A:0x007f9c358353f0>
  end
end

A.new.hello
```

**variables**

```ruby
class B
  @a = 1
  @@b = 2

  def initialize
    @c = 3
    @@d = 4
  end

  class << self
    @e = 5
    @@f = 6
  end

  def a
    @a
  end

  def c
    @c
  end

  def self.a
    @a
  end

  def c_variables
    [@@b, @@d, @@f]
  end

end

b = B.new
p B.instance_variables #=> [:@a]
p B.class_variables #=> [:@@b, :@@f, :@@d]

p b.instance_variables #=> [:@c]

p B.singleton_class.instance_variables #=> [:@e]
p B.singleton_class.class_variables #=> []
```

规律与结论：

* 类的类变量可以读取到：类作用域 实例作用域 单例类作用域
* 类的实例变量可以读取到：类的作用域
* 实例不存在类变量
* 实例的实例变量只能读取到：实例作用域
* 单例类的实例变量可以读取到：单例类作用域
* 单例类的类变量为空

1. 类变量：类的类变量读取三个作用域
2. 实例变量：实例变量读取各自作用域的实例变量

## method_missing & define_method

**method_missing**

方法查找路径：

1. 类内部找方法名
2. 类内部找method_missing
3. 继承链上一层找方法名
4. 继承链上一层找method_missing
5. ...

可以分别为类方法或实例方法定义method_missing

```ruby
class << self
    def method_missing method_name, *params
        method_name = method_name.to_s

        if method_name =~ /=$/
            @@attributes[method_name.sub('=', '')] = params.first
        else
            @@attributes[method_name]
        end
    end
end
```

**define_method**

可以分别为类方法或实例方法定义define_method

示范：

```ruby
class User < ActiveRecord::Base
  STATUS = %w[pending activated suspended]

  STATUS.each do |status|
    define_method "is_#{status}?" do
      self.status == status
    end
  end
end
```

## class_eval & instance_eval

**class_eval**

1. 打开类作用域
2. 接收do end块
3. 定义方法为实例方法
4. 定义self方法为类方法
5. 对比`class_exec`:
   1. eval支持字符串和代码块；exec不支持字符串，只支持代码块
   2. 只能通过eval修改作用域内部的常量以及变量，因为只能通过字符串

6. `module_eval`可以理解为别名方法

**instance_eval**

1. 打开单例作用域
2. 接收do end块
3. 类调用定义方法为类方法，实例调用定义方法为实例的单例方法
4. 对比`instance_exec`：同上

class_eval与include举例示范

```ruby
module B
  def self.included base
    p 'included...'

    base.class_eval do
      set_logger :hi
    end
  end

  def hi
    p 'hi'
  end
end

class C
  def self.set_logger method_name
    # .....
  end

  include B
end
```

## 设计模式之Module Mixin

**组织方式一**

```ruby
module ActsAsField

  def self.included base #回调 base为引入该模块的类
    base.include InstanceMethods #引入实例方法
    base.extend ClassMethods #引入类方法

    base.class_eval do
      @@acts_as_fields = []
    end
  end

  module ClassMethods #定义类方法
    def field 
    end
  end

  module InstanceMethods #定义实例方法
    def acts_as_fields
      self.class.class_variable_get :@@acts_as_fields
    end
  end

end

class Device
  include ActsAsField
end
```

**组织方式二**

```ruby
require 'active_support/concern'

module ActsAsField
  extend ActiveSupport::Concern
  #extend EggConcern
    
  included do #注入的类作用域中执行
    @@acts_as_fields = []
  end

  class_methods do #定义类方法
    def field 
    end
  end
    
  module ClassMethods #定义类方法2
    def filed
    end
  end

  #定义实例方法
  def acts_as_fields
    self.class.class_variable_get :@@acts_as_fields
  end

end

class Device
  include ActsAsField
end
```

**append_features**

作用：钩子方法。如果有定义一个self.append_feature类方法的模块被include进一个类或模块的时候执行，与self.included类似。

示范：EggConcern实现：

```ruby
module EggConcern
  def append_features base
    super
    base.instance_eval(&@_class_methods)
    base.class_eval(&@_class_eval)
  end

  def included base = nil, &block
    super
    @_class_eval = block
  end

  def class_methods &block
    @_class_methods = block
  end

end

extend后变为self.append_features
```

