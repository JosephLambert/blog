# Rails高级开发（其他）

## Rails代码组织

* concern目录，放置可以重复利用的共享功能，模块化代码。
  * controllers/concern
  * models/concern
* lib目录，放置业务和非业务相关的功能完整的代码。
* fat model：让controller轻量化，把业务逻辑放在model
* 其他技巧：presenters、services

## 前端性能分析

**前端性能分析**：BS模式

* 按照DOM tree的加载顺序解析和加载
* 资源分类：
  1. DOM文档：页面自身
  2. CSS：link标签，重新发起请求下载并渲染
  3. Javascript：script标签
  4. Image：img标签
  5. iframe标签

* 浏览器并发加载：浏览器对同一个domain下的资源支持有数量限制（小于8）的并发下载
* 多Domain策略：针对资源使用不同域名，变相增加浏览器并发加载数量，减少cookies信息的传输数据量
* 浏览器事件：
  * DOMContendLoaded：加载完DOMtree之后
  * Load：加载完CSSJSIMG资源后

* Javascript的特殊性：JS文件的加载和执行会阻塞其他资源的加载，因为JS会修改DOM。JS文件默认加载也是线性的。

**前端性能优化**

- Javascript加载应对措施：
  - 异步加载：script标签中async属性，JS下载变为并行
  - 推迟执行：defer属性，下载之后默认立即执行，变为暂时不执行
  - <u>动态创建script标签</u>：document.createElement('script')
  - 使用setTimeout()函数来延迟加载

- DOM加载顺序优化：
  - 优化用户体验：优先加载CSS，最后加载JS
  - 方式：把link标签放在head标签中，把script标签放在body结束前

- 其他优化措施：
  - Network Level：DNS（稳定+响应时间+容错）+ IP Anycast
  - Webpage Level：
    1. 减少HTTP请求，combine CSS and JS，image sprite sheets
    2. 降低响应内容，minify CSS and JS，ajax/turbolinks
    3. image lazy load，只加载用户能看到的部分，边滚动边加载
    4. 改善资源下载速度：reduce asset size+asset cache control+CDN

## Asset Pipeline

* 是管理和部署静态资源的一种方式
* 由sprockets-rails gem来完成，Rails4默认支持
* 静态资源放置：
  1. app/assets：与项目有关的静态资源
  2. lib/assets：可以通用的资源
  3. vendor/assets：第三方的资源

* gem 依赖：
  * sass-rails，用scss拓展css，压缩css
  * coffee-rails，用coffee拓展js
  * uglifier，压缩js
  * therubyracer，解释js的V8引擎
  * execjs，提供了不同的js运行环境
* CSS/JS组织方式：模版application.html.erb引入application.js/scss，其中再用特定语法引入需要的文件。`//= require_tree .`会自动引入javascript文件夹下的所有的js文件。
  * application.js：`//= require welcome`
  * application.scss：`@import "welcome";`可以用erb

* 设置静态资源主机地址：config.action_controller.asset_host
* asset_path/url可以在三个文件夹下寻找文件并输出path
* 环境：生产需要配置，开发无需配置因为频繁改动
* rake assets:precompile，预编译之后放在public下，供生产nginx直接使用。

## Rails环境

**Rails的启动与加载**

<u>启动</u>

* 开发环境-rails命令启动：bin/rails->application.rb->boot.rb->gemfile 
* 生产环境-rake脚本启动：config.ru->environment.rb->application.rb->boot.rb->gemfile 

<u>加载</u>

1. 启动过程：bundle gems
2. 项目代码：app/* lib/* config/*
3. config/ initializers：作用同application.rb中的配置；应用启动过程中只加载一次，修改需要重启server

**Rails的环境**

<u>默认的三个环境</u>：

1. test
2. development
3. production

<u>配置文件</u>：

1. config/environments/*.rb 优先级更高
2. config/application.rb

<u>配置方式</u>：

* config.*
* Rails.application.config.*

<u>配置选项</u>：

* autoload_once_paths
* autoload_paths
* cache_classes
* consider_all_request_local
* filter_parameters
* middileware：在rails接受请求之前的前置处理逻辑

<u>自定义配置</u>：

* 定义：config.xxx.yyy = true
* 使用：Rails.configuration.xxx.yyy  #=>true

**如何添加staging环境**

1. config/environments/staging.rb
2. database.yml->staging: 
3. secrets.yml->staging:

**添加自定义配置文件application.yml**

1. application.yml
2. config/initializers/app_config.rb 引入yml文件

`AppConfig = YAML.load_file("#{Rails.root}/config/application.yml")[Rails.env] `

3. 使用：AppConfig[domain'']在不同环境下读取不同数据

## ActiveSupport

* 源自于gem activesupport
* 拓展了ruby的内置类，提供了更多底层功能
* rails默认加载了该gem中所有拓展
* 手动添加：全部：require 'active_support/all'；也可以按模块添加：require 'active_support'以及require 'active_support/core_ext/object/blank'

## ActionMailer

* 用来发送email
* SMTP协议规定<u>发送邮件</u>的协议，<u>接受邮件</u>是POP以及IMAP协议
* 像使用controller/view一样发送邮件
* 配置：application.rb，设置SMTP服务器账户信息

```ruby
config.action_mailer.smtp_settings = {
      :address => "smtp.example.com", 
      :port => 25,
      :domain => "343edu.com",
      :user_name => "username",
      :password => "password",
      :authentication => :login,
      :enable_starttls_auto => false
    }
```

* 通常用第三方邮件服务器作为企业邮箱，国外用AWS的ses、gmail
* 目录：
  * app/mailers:application_mailer.rb
  * app/views

* 生成mailer：rails g mailer user_mailer
* view注意事项：使用绝对地址
  * config.action_mailer.default_url_option = { host: 'example.com' }
  * welcome_url

* email类型：signup.html.erb  signup.text.erb

* 发送email：UserMailer.signup(@user).deliver_now（同步）

## 异步任务

* 将逻辑复杂以及耗时的任务设置成异步以及回调获取结果的方式来减少等待时间
* 资源或时间占用多的任务
  * 邮件发送
  * 批量数据处理
  * 数据导出
  * 定时任务
  * etc

* 方案设计：进程（主流） 线程（依赖于进程而且还有线程安全的问题）
* 解决方案：
  * Sidekiq 基于Redis
  * Resque 基于Redis
  * Delayed Job
  * Background Job

* gem Active Job，实现了统一的异步任务接口，<u>需要结合</u>第三方的异步任务组件

## Rails Security

**Session & Cookies**

* cookie中的数据不要放敏感信息
* 基于cookie的session是加密的，比较安全，user_id

**User Input**

* CSRF-RESTful/csrf_token
* XSS-escape/sanitize白名单
* File download/upload-不能由文件名来操作路径，只能由代码控制路径
* System command - system（）由用户输入运行shell命令，设置白名单
* HTTP request methods - RESTful设计原则
* Redirection - 不能把用户输入执行，可以作为条件判断
* Regular Expression - /^$/（传统表达式-一行） vs /\A\Z/（ruby中-多行）

**Database**

* mass assignment-(gem protect attribute)attr_accessible(白名单),attr_protected(黑名单)/params.require.permit
* SQL injection-use native method/sanitize sql
* unscoped finds-权限限制 Blog.find vs current_user.blogs.find
* batch update - update_all性能更好

**Configuration**

* running environments
* passwords - 禁止明文存储 以及 不要写入log
* filters logs parameters - log/backtrace
* routes - 禁止 default routes

**Rails & Lib bugs**

* 关注最新安全相关的信息

## 测试&RSpec

**软件测试**

* 保证软件的质量和可用性，尽可能减少bug和性能问题
* 保证软件的可持续集成：新功能&重构&版本升级之后，针对以前功能是否有影响
* 潜在帮助分析和完善产品需求：从代码角度分析产品需求
* 给团队带来信心

**软件测试方式**

* 黑盒测试，需要考虑极端情况
* 白盒测试

**软件测试内容**

* 单元测试（开发负责）：Rspec/Minitest/UnitTest
* 集成测试：Cucumber/Selenium/Capybara

**测试基础概念**

* 测试用例 test case
* 边缘用例 edge case
* 测试覆盖率 coverage rate
* 伪造 mock

**Rspec安装**

* http://rspec.info
* gemfile: gem 'rspec-rails', ' -> 3.0' 
* 分为4个子gem：
  1. rspec-core
  2. rspec-expectations
  3. rspec-mocks
  4. rspec-rails
  5. rspec-support

* test运行环境：每个测试用例前都会清空db

**Rspec学习**

* 文件：/spec（测试文件） & /.rspec(执行命令参数)
* 命名约束：blogs_controller_spec.rb
* 测试编写：
  1. describe BlogsController do 指明测试的类
  2. context "test_content" do 像是命名空间描述测试内容，可以嵌套
  3. before(:each) do 每个测试用例之前都要运行的块，其中的实例变量@可以在it中读取到
  4. it "test case content" do 具体的测试用例
     * get :edit, id: 1
     * 断言expect(response).to redirect_to(path)
     * 伪造expect(@blog).to receive(:save).and_return(false)保持测试用例变量唯一性

* 运行测试：rspec  *.rb:6 (it)

