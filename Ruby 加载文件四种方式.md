## Ruby 加载文件四种方式



Ruby中加载一个文件有四种方式，`require`、`require_relative`、`load`、`autoload`。



**require和require_relative**

`require`是Ruby中最常见的加载一个文件的方式，如调用`require 'rails'`时，会`$LOAD_PATH`下寻找名为`rails`的`gem`包，然后将其`lib`文件夹下的同名文件加载到内存中，**加载到内存过程中会执行一边代码**，相当于把要加载的文件的代码替换到 require 处。多次require同一个包，只会加载一次。

`require_relative`与`require`类似，它只会在第一次调用时加载。区别是`require_relative`的调用是相对路径。如当前文件夹下存在一个名为`foo.rb`的文件时，调用的方式为`require_relative 'foo'`。`它不能调用$LOAD_PATH中的包`。

大约是Ruby2.0以后，`require`也支持了相对路径的加载。比如上面的`foo.rb`在当前目录时，通过`require './foo'`也能达到`require_relative 'foo'`的效果。



**load**

`load`也是加载一个文件，它与`require_relative`的区别是：

 `require_relative`多次加载同一文件时，只会加载一次；`load`每一次调用都会重加载该文件。



**autoload**

`autoload`是一种重要的加载方式，与`require`的区别是`require是即时加载`，`autoload`的加载是`懒加载`，即在需要它的时候才会被加载。`autoload`在某一个作用域内多次加载也只会被加载一次，因此不要以为它与load方法相似。

如果当前路径下有`a.rb`、`b.rb`两个文件：

```ruby
class A
  autoload :B, './b'
  # 与 require './b' 等价，但autoload只有在调用 A::B 的时候才会去加载
end
```

`autoload`第一个参数是类名的符号，第二个参数是加载的路径。它同时支持加载`$LOAD_PATH`里的文件和`相对路径`、`绝对路径`的文件。

`Rails`中重定义了`autoload`方法，补充了一下path为空的情况下的常量寻找方法：

```ruby
class Foo
  require "active_support/all" 
  extend ActiveSupport::Autoload 
  autoload :B, "b" 
end

class Bar
  require "active_support/all" 
  extend ActiveSupport::Autoload 
  autoload :B
end
```

`Bar`中的`autoload`没有指定加载文件的路径，`Rails`会自动生成加载路径`bar/b`，而`Foo`中的路径已指明，加载的路径是`b`，因此两者加载的路径是不一样的。这是`Rails`中`autoload`与`Ruby`中`autoload`的区别。

