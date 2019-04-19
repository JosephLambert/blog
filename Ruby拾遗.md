## Ruby拾遗

### bundle exex 做了什么

bundle是管理gem的gem，解决了版本依赖问题。

例如，直接运行命令`rake -T`，会报错显示你已经激活了一个版本，但是gemfile中需要的是另一个版本。此时命令默认去寻找系统中安装的最新版本的对应的可执行程序，跟bundle（来源gemfile）中依赖的版本冲突。

`bundle exec`实际上修改了系统环境变量，最终使用gemfile中指定的gem版本。

### 怎样在Ruby项目中独立使用Bundler

1. mkdir ruby_project

2. cd ruby_project

3. bundle init 生成gemfile

4. 修改gemfile中的source以及添加需要使用的gem 例如 activesupport

5. 运行 bundle install

6. 写ruby程序

   ```ruby
   # boot.rb
   ENV['BUNDLE_GEMFILE'] ||= File.expand_path('../Gemfile', __FILE__)
   
   require 'bundler/setup' # set up gems listed in the Gemfile
   
   # main.rb
   require File.expand_path('../boot', __FILE__)
   require 'active_support/all'
   
   # require your own libs here
   p 1.days
   
   #run:
   ruby main.rb
   ```

### 如何把method当作参数传递

```ruby
def hi name
  p "hi" + name
end

# 方式一
method(:hi) # 返回Method类的一个实例 可以当作参数传递
method(:hi).call('yanlei') # 真正执行

def hi_back1(m)
  m.call('yanlei')
end

hi_back1(method(:hi))

#方式二 更普遍
def hi_back2
  yield
end

hi_back2{ hi('yanlei') }
```

### 怎样创建，测试和发布自己的Gem

**创建**

`bundle gem gem_test` 用bundler创建一个gem需要的的目录结构

**在irb中测试gem**

1. 进入gem目录

2. irb

3. 添加自定义的gem到ruby的load_path数组中  `$: << File.expand_path('./lib')`

4. 加载入口文件：`require 'gem_test'` 

5. 如果需要调试，重新加载：irb中

   ```ruby
   def reload(gem)
     $".grep(Regexp.new(gem)).each { |file| load file }
   end
   reload（'gem_test'）# 之后方法刷新
   ```

6. 然后就可以使用gem中定义的类和方法

**在Rails中结合bundle测试gem和实现热加载**

1. gem_test和project位于同一个文件夹下

2. project gemfile添加：gem 'gem_test', path: File.expand_path('../../gem_test', \_FILE\_)

3. bundle 安装

4. rails c中直接调用类和方法

5. 如果此时修改了gem中的方法，内存不会探测gem中的文件是否有修改以及对文件重新加载。

6. config/environment/development.rb

   ```ruby
   RELOAD_GEM = lambda do |gem|
     $".grep(Regexp.new(gem)).each { |file| load file }
   end
   
   config.reload_classes_only_on_change = false # 改为每次请求都会重新加载不论是否有修改
   # 还可以把gem_test中的文件 加入 autoload_path 同样会探测是否有修改代码
   
   config.to_prepare do #代码有改动会触发该逻辑
     p 'reload gem ...'
     RELOAD_GEM.call('gem_test')
   end
   ```

**发布**

1. `rubygems.org`注册账号
2. gem_test目录 首先要提交git，注意VERSION
3. rake build 打包gem
4. rake release 发布到rubygems，第一次需要输入账号和密码