## Rails 5源码分析

### autoload & ActiveSupport::Autoload源码分析

命令`bundle show activerecord`返回gem所在系统中的路径，可用sublime打开gem源码的文件夹。

**autoload应用示范**：

1. require 'active_support'
2. extend ActiveSupport::Autoload
3. autoload :Attribute

**为autoload方法设置可执行的路径**：

`config.autoload_paths += %W(#{Rails.root}/lib)`

**autoload作用以及对比**：

1. `require`作用是把代码运行一遍加载到内存。
2. **开发环境中**，出于性能考虑，为了不一次性加载所有文件，`ruby原生的autoload`方法实现了按需加载或延时加载的功能：需要该常量的时候判断该常量在内存中是否存在，不存在则从指定路径中读取并加载。该方法有两个参数，一个常量名，一个文件路径。**生产环境**中，是一次性加载所有代码。
3. `ActiveSupport的autoload`相比ruby的autoload，super之外完成了类名和路径的约束转换，该方法可以只有一个常量名参数。
4. `ActiveSupport的eager_autoload`指定的目录中的文件会马上执行加载到内存，类似require。利用了约束转换的功能。配置：`config.eager_load_paths = ` 默认app下的所有文件夹。

### ActiveRecord Query源码分析

`User < ApplicationRecord < ActiveRecord::Base`

**active_record/base.rb**入口文件中：

```ruby
require '...'
module ActiveRecord
  class Base
    extend Querying
    include Scoping
  end
end
```

**active_record/querying.rb**中：

`delegate :where, to: :all`

**active_record/scoping.rb**中：

```ruby
module ActiveRecord
  module Scoping
    extend ActiveSupport::Concern
    included do
      include Default
      include Named #=> all方法定义
    end
    module ClassMethods
      ...
    end
  end
end
```

**查询机制**：

where语句只是封装了Relation对象，当记录没有真正被调用的时候，是没有执行数据库查询的。在rails console中查询显示了数据库查询，是因为打印结果默认调用inspect方法，该方法会执行数据库查询。

### ActiveRecord Callbacks源码分析

回调机制来源**active_support/callbacks.rb**，使用示范：

```ruby
class Record
  include ActiveSupport::Callbacks #引入模块
  define_callbacks :save #注册自定义事件

  def save #定义事件内容
    run_callbacks :save do
      puts "- save"
    end
  end
end

class PersonRecord < Record
  set_callback :save, :before, :saving_message #注册回调
  def saving_message #定义回调
    puts "saving..."
  end

  set_callback :save, :after do |object| #注册并定义回调
    puts "saved"
  end
end

 person = PersonRecord.new
 person.save #出发自定义事件以及回调
```

启发：可以独立使用回调机制，自定义自己的事件以及回调

### Rails::Application源码分析

**config/application.rb**中：

```ruby
module Rails5Digging
  class Application < Rails::Application
    config.load_defaults 5.2
  end
end
```

Rails::Application 是最顶层的类，涉及rails启动过程。

**config/environment.rb**中：

```ruby
# Load the Rails application.
require_relative 'application'

# Initialize the Rails application.
Rails.application.initialize!

# Rails.application 是 Rails5Digging::Application类暴露出来的一个实例
```

**Rails::Application类在gem railties中，在rails/application.rb中注释了rails启动过程**

```ruby
  #   1)  require "config/boot.rb" to setup load paths
  #   2)  require railties and engines
  #   3)  Define Rails.application as "class MyApp::Application < Rails::Application"
  #   4)  Run config.before_configuration callbacks
  #   5)  Load config/environments/ENV.rb
  #   6)  Run config.before_initialize callbacks
  #   7)  Run Railtie#initializer defined by railties, engines and application.
  #       One by one, each engine sets up its load paths, routes and runs its config/initializers/* files.
  #   8)  Custom Railtie#initializers added by railties, engines and applications are executed
  #   9)  Build the middleware stack and run to_prepare callbacks
  #   10) Run config.before_eager_load and eager_load! if eager_load is true
  #   11) Run config.after_initialize callbacks
```

**rails/engine.rb中有call方法**：

这是rack middleware要求定义的方法，rake middleware作用是是处理HTTP协议底层。Rails也是一个rack，所以也定义了call方法。

### ActionController::Base filter源码分析

**实现机制**

核心是ActiveSupport::Callbacks

**AbstractController::Callbacks中定义的回调**before-after-around

1. before_action
2. skip_before_action
3. prepend_before_action
4. append_before_action

**rails console中查看回调链**

`WelcomeController._process_action_callbacks.send(:chain)`

### Rack Middleware

**Rack作用**（gem railties）

负责在分发给controller以及action之前，处理HTTP协议底层操作，例如请求头部信息分析、以及响应头部信息的组织。各个middleware模块化，各有分工。

**有哪些middleware**

`Rails.application.middleware`返回一个中间件数组，从上至下流水线按顺序执行。`默认的最后一个中间件是ActionDispatch::Routing::Routeset`，负责在一系列中间件解析完HTTP请求后，路由解析以及分发给controller以及action，该中间件不显示在数组中。

**middleware接口要求与自定义示范**

```ruby
# lib/test_middleware.rb
class TestMiddleware

  # 中间件必须定义的两个方法
  def initialize app # app是下一个中间件的实例
    @app = app
  end

  def call(env) #env是hash
    time_start = Time.now

    # 让下一个middleware执行并返回结果
    status, header, response = @app.call(env)

    # 该middleware作用
    time_end = Time.now
    duration = time_end - time_start
    Rails.logger.info "TestMiddleware: duration: #{duration}"

    [status, header, response] #返回数据格式要求每个元素都可以响应each方法
  end

end
```

**如何配置使用自定义的中间件**

```ruby
# config/application.rb
require_relative '../lib/test_middleware'
module Rails5Digging
  class Application < Rails::Application
    config.middleware.use TestMiddleware
  end
end
```

