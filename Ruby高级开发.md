# Ruby高级开发

## Ruby特点

优点

* 解释型语言：无需编译，边解释边执行
* 泛型：无需显示声明变量类型
* OOP：Ruby中一切都是对象
* Module：模块更加丰富了Ruby对面向对象的支持
* 反射：运行代码时反向知道代码内部情况
* 垃圾回收：自动运行
* 为程序员而生，而不是为计算机
* 轻量级
* 跨平台

缺点

* 性能问题：性能是肯定有一些差距的。但是互联网中是需求为导向的，需求改动很频繁，我们会更关注产品本身，而不是语法、算法的实现。而且性能方面的问题可以通过升级硬件、优化其他组件例如异步、数据库、第三方API调用来优化。
* 团队合作：语言非常灵活导致同一个目标可以有很多实现方式，可能不方便别的程序员理解。约束大于配置：命名规范，常用的设计模式，代码书写规范。

与Python相比：

* 更加OOP
* 更加动态和灵活

## Ruby安装

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fv3dkssnwzj30u60g9tc3.jpg)

**常用命令**

* `rvm list known`：列出所有运行环境的所有ruby版本
* `rvm install 2.2.2`：安装指定版本的ruby
* `rvm use --default 2.2.3`：设置指定版本为系统默认版本
* `rvm use 2.2.2`：切换其他版本
* `rvm uninstall 2.2.1`：写在卸载一个指定版本

**CLT**

* irb：交互式ruby解释器
* rdoc：ruby 文档

**在线文档以及软件**

* https://apidock.com/ruby
* http://ruby-doc.org/core-2.4.1/
* https://api.rubyonrails.org/
* Dash

## Ruby基础

**基础数据类型**

* fixnum
* float
* string
* boolean
* array
* hash
* symbol 
* range
* regular expression

**区分**

```ruby
a = "hello"

a.replace("hello2")
a = "hello2"
```

前者不会改变`object_id`，后者会改变object_id

**nil**

nil跟false都是不成立，其他一切都是true

**定义字符串、数组的特别的方式**

```ruby
%i[id name] #=> [:id, :name]

%w[id name] #=> ["id", "name"]

%Q{} #相当于双引号定义字符串，保留空格和换行

%q{} #相当于单引号定义字符串，保留空格和换行

<<-HERE
...
HERE
#同%Q{}
```

**数组与哈希的顺序**

* 哈希是无序的
* 数组是有序的

**Boolean表达式**

* && || ！
* and or not，`优先级低于=以及&&||！`
* 运算机制：有确定结果后续代码不执行，返回值可以用来赋值
* 优先级的问题可能带来bug，不建议用and or not

**符号Symbol**

* 符号是字符串的补充，是轻量级的字符串
* 可以当作字符串来使用
* 两者本质区别在于，同一个符号的object_id唯一
* 应用：hash的key或者一些变化不频繁的字符串中使用

**正则表达式Regular Expression**

```ruby
a = /hello/
"hello world" =~ a #返回匹配到的首字符的索引或者nil
"hello world" !~ a #没有匹配到返回true，匹配到返回false
a.match("hello world")se
```

**变量类型**

* 局部变量：`a = 1`，作用域：方法内部
* 常量：`Names = 'yan'`，常量定义之后再赋值会警告，安全的方法是：`Name.replace('yan2')`
* 全局变量：`$platform = 'Mac'`，作用域：任何地方
* 实例变量：`@name = 'yan'`，作用域：实例方法内部
* 类实例变量：`@@counter = 20`，作用域：类内部（实例方法内部、类方法内部、类内部）

## 方法method

**以！结尾的方法**

- ruby中代表会改变调用者本身
- rails中代表会抛出异常
- 这只是一个命名约束，并不是符号本身起作用

**以？结尾的方法**

- 代表该方法会返回true或者false
- 同样，这只是一个约束

**方法的参数**

方法的定义和使用时，参数传递都是可以不使用（）

**为方法的参数设置默认值**

```ruby
def hi(name='jo')
    p "hi #{name}"
end
hi
```

不设置默认值的话，就必须要传参数

**方法的唯一性**

完全取决于方法名，方法名一样的两个method，即使其他不一样，后一个也会覆盖前一个。

**方法的返回值**

* 所有方法都有返回值
* 默认最后一行代码的返回值为方法的返回值
* 可以显示用`return`指定返回值

**特别的方法**

* send：可以调用私有方法或者参数化方法（符号）
* respond_to？：是否可以响应某个方法（符号）
* class_eval：类才可以调用，打开当前类作用域
* instance_eval：实例就可以调用，打开当前实例作用域。普通实例打开定义的是单例方法，类打开定义的是单例类方法 
* method_missing：沿着继承链找方法，没有方法时，调用定义的method_missing方法
* alias：`alias old_hello hello` 给hello方法取了一个别名 方法old_hello

## 块block

**内涵和定义**

* 块类似于方法，是操作的集合，可以传进来块参数
* 块对象常被作为参数传递或返回值返回
* 匿名方法
* callback，有一些特殊逻辑需要定制
* 通过`do end` 或者`{ }`定义

**块作用域**

* 块内变量只属于块内
* 块内可以读取到块外变量
* closure特性

**定义与使用带块方法**

方式一：

```ruby
def hi(name)
    yield(name)
end
hi('code') { |x| "hello #{x}" }
```

`yield`为调用外部块的关键字，可以传入参数并且传出返回值

方式二：

```ruby
def hi(name, &block)
    block.call(name)
end
hi('code') { |x| "hello #{x}" }
```

两者的差异在于：方式二可以把块作为对象或者参数继续传递或者显示指明可以传递块

另外，`block_given?`可以判断是否传入的块，块是可以不传入的。

**定义与使用块对象**

方式一：

```ruby
hi = proc { |x| "hello #{x}" }
hi.call('world')
```

等同于

```ruby
hi = Proc.new { |x| "hello #{x}" }
hi.call('world')
```

方式二：

```ruby
hi = lambda { |x| "hello #{x}" }
hi.call('world')
```

等价于

```ruby
hi = ->(x) { "hello #{x}" }
hi.call('world')
```

**lambda和proc区别**

* lambda更像是方法，对参数要求更严格
* proc对参数要求不严格

## 流程控制语句

**条件**

* `if elsif else end`
* `unless` 等价于 if not
* `case when when else end`when后可以是逗号并列条件，可以使正则表达式，可以是class

**循环**

* `while 条件 { }`条件为真时执行块，`until`与之相反
* `loop do end`会一直循环，需要配合break使用
* `for x in [1,2,3] { }`
* `[1,2,3].each do |i| end`
* `10.times do |t| end`

**循环控制**

* break
* next  

## 异常处理Exception

所有异常都是继承于Exception类

**常见的异常类**

* StandardError
* SyntaxError
* RuntimeError：raise的默认异常类
* ArgumentError
* NameError

**抛出异常**

`raise`

**异常处理**

```ruby
begin
    可能出错的代码 
rescue RuntimeError #指明异常类
#rescue => e 将异常放进e对象
	捕获异常之后要执行的代码
else
    如果没有异常执行的代码
ensure
    无论是否有异常都要执行的代码
end
```

## 继承和模块

**继承**

子类继承父类：获得父类的方法、拓展父类的方法、覆盖修改父类的方法

调用父类（继承链的上一级，包含模块）的同名方法的关键字：`super`

查看父类的方法：`User.superclass`（不包含模块）

查看继承链（定义了方法查找顺序）的方法：`User.ancestors` (包含模块)

**模块**

*作用* 

* mixin：Ruby不支持多重继承，但是可以用模块来实现类似于多类继承的功能
* 命名空间：模块 类 常量，用`::`来访问
* storage

*关键字*

* module声明一个模块
* include把一个模块的普通方法注入为实例方法，include多个模块时，后声明引入的模块在继承链中更靠近类本身
* extend把一个模块的普通方法注入为类方法
* prepend与include类似注入为实例方法，但是prepend在继承链中模块在类的下方，而include在上方

*直接使用module中的方法*

* self方法可以module_name.self_method调用
* 普通方法需要先include module_name之后在直接调用normal_method

*class与module的关系*

- module本质上也是class，是不能实例化的class、特殊的class：普通模块的class是Module，Module的class是Class。
- class是添加了实例化功能的module
- class和module都可以*重新打开*，方法可以重新定义覆盖

*included方法*

应用：include一个模块的时候分别注入实例方法和类方法

```ruby
module Management
  def self.included base
    puts "Management is being included into #{base}"

    base.include InstanceMethods
    base.extend ClassMethods
  end

  module InstanceMethods
    def company_notifies
      'company_notifies from management'
    end
  end

  module ClassMethods
    def progress
      'progress'
    end
  end

end

class User
  include Management
end
```

应用：include一个模块的时候同时执行某些方法

```ruby
module Management

  def self.included base
    base.extend ClassMethods

    base.class_eval do
      setup_attribute
    end
  end

  module ClassMethods
    def setup_attribute
      puts 'setup_attribute'
    end
  end
end

class User
  include Management
end
```

**self**

*指向当前作用域的实例本身*：

* 实例方法的作用域中，代表类的实例对象
* 类方法的作用域中，代表类本身

*方法是否需要显式指明self，取决于变量查找逻辑*：

1. 先找方法定义内部的局部变量（*如果有局部变量，不可以省略self，否则会调用该局部变量*）
2. 再去找实例方法中是否有同名方法



## 面向对象OOP

**基础概念**

* 是一种设计模式
* 对象、属性、方法
* 所有都是对象
* `obj.class`查看对象所属类

**实例方法和实例变量**

* 类实例化后的对象可以调用的方法叫做实例方法
* 可以访问实例方法作用域的变量叫实例变量，用@定义
* `attr_reader :name`等价于`def name`可以读取`@name`
* `attr_writer :name`等价于`def name=`可以赋值`@name`
* `attr_accessor`等价于`attr_reader`加上`attr_writer`

**类方法和类变量**

* 类方法是类的方法，不会随着类的实例不同而不同。类方法本质是把类当作一个对象定义的单例方法。类的类是Class类。 
* 类变量是类内部作用域可以访问的变量，用@@来定义
* 定义方式：
  1. `def self.get_counter end` 

  2. 

     ```ruby
     class User
         class << self
             def get_counter
                 ...
             end
         end
     end
     ```


**访问控制**

|           | 从类以及子类内部访问 | 从实例对象外部访问 |
| --------- | -------------------- | ------------------ |
| public    | 可以                 | 可以               |
| protected | 可以                 | 不可以             |
| private   | 可以（只能隐式）     | 不可以             |

**Ruby的动态特性**

define_method：

```ruby
define_method :hello do
  puts "hello world"
end

hello

define_method :hello do |name|
  puts "hello #{name}"
end

hello("yan")
```

## Ruby拾遗

**代码书写规范**

- UTF-8编码
- 使用空格缩进，不使用tab，1tab = 2 spaces
- 不需要使用`分号；`以及`反斜杠\`连接代码 
- `等号=`左右有空格
- 数组的元素`逗号，`后加空格
- key-value的`冒号：`后加空格
- 哈希的花括号内部各一个空格
- 括号内部无空格  

**Ruby代码加载机制**

- $LOAD_PATH：全局变量，定义了当require一个文件时，搜索加载ruby文件的路径；如果没有找到，报出异常。
- 该变量是数组，$LOAD_PATH <<  新路径
- 命名约束：文件名用蛇底式并且文件内容有定义其对应驼峰式的类或模块 

**require vs load**

相同点

* 都会在`$LOAD_PATH`下面查找当前要引入的文件

不同点

* require调用文件时不需要指明文件后缀，而load需要，不加会报错
* require针对同样的文件只会调用一次，之后会调用失败返回false，而load会反复调用

**Gems**

* Ruby的包管理工具
* 社区中的Gem包涵盖了各种开发所需的工具和组件
* 墙内安装使用淘宝的镜像地址：`https://ruby.taobao.org`
* gem命令
  * gem list
  * gem install
  * gem uninstall
  * gem sources

**bundle gem**

* 解决来gem本身的版本依赖问题
* bundle是一个gem，作用是管理其他gems
* bundle的基本使用
  * gem install bundler
  * Gemfile文件
  * bundle install/update/show/list

**Debug工具 byebug** 

```ruby
$ gem install byebug

require 'byebug'

def hello
    a = "hello"
    byebug
    puts a
end 
```

**进程和线程**

Process：独立占用cpu以及内存资源，运行独立功能

```ruby
pid = Process.fork {} #创建进程
```

Thread：进程分发出来的，共享进程变量，并行执行代码

```ruby
Thread.new {}
```

GIL:GLobal Interpreter Lock，操作系统底层的限制，并不能让线程完全并行执行 。

线程安全：线程可以完全并行执行。不会出现变量竞争的情况。