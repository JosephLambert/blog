



# 	Rails高级开发（MVCR）

## Rails介绍

**Ruby on Rails 是什么**

* web开发框架
* 基于ruby
* 2004/07DHH开发的

**Rails设计哲学**

* 遵循web标准以及HTTP协议
* COC约定大于配置
* MVC
* DRY不要重复造轮子
* 软件工程设计模式
* Assets静态资源管理
* Restful路由与API
* Testing测试
* Deployment部署
* Automation自动化

<u>敏捷开发快速迭代</u>：创意或想法->线上-〉修改-〉线上-〉下次迭代

<u>从本地开发到线上</u>：

* 环境：Development/Test/Production/Staging
* Test
* Configurations
* Deployment

<u>Dynamic Pages</u>：

* Routes
  * HTTP GET/POST/DELETE/PUT
  * custom routes：例如默认参数、命名空间、请求方式、正则匹配
  * easy to refactor
* Restful
* Cache：Page Cache & Fragment Cache
* Response：HTML JSON XML etc. 
* Inheritance
  * Controller层：filter
  * Model层：ORM & trigger

<u>Assets</u>：

* 工具：Asset Pipeline、Minify、Combine、Webpack、babel
* Js/CSSminify and combine
* Assets URL
* Etag
* SASS Coffeescript

 <u>测试</u>

* 单元测试：rspec/minitest
* 集成测试：cucumber/selenium

<u>脚本和自动化</u>

* rails c/s/g
* rake task：rake db:create/migrate、rake routes以及自定义rake任务

<u>libraries</u>

* gem
* bundle
* rails是很多gem的集合
* 可以创建自己的gem

**MVC设计模式**

![](https://ws3.sinaimg.cn/large/006tNc79gy1fvbe7ygf5mj309z0b60tx.jpg)

## Model

**ORM**

对象关系模型，Active Record实现数据记录与对象的映射，数据表与模型的映射

**Rails对数据库的支持**

<u>ActiveRecord对关系型数据库的支持</u>

* SQLite：Rails默认数据库
* MySQL
* PostgreSQL
* Oracle
* SQLServer

<u>对非关系型数据库的支持</u>

* MongoDB（需要gem Mongoid/MongoMapper）
* Redis
* HBase

**引入MySQL数据库**

1. 本地电脑安装MySQL数据库

2. gem 'mysql2'

3. database.yml：

   ```yaml
   default: &default
     adapter: mysql2
     encoding: utf8
     pool: 5
     host: 127.0.0.1
     username: root (root账号才能执行 rake db:create)
     password:
   
   development:
     <<: *default
     database: circles_development
   
   test:
     <<: *default
     database: circles_test
   
   production:
     <<: *default
     host: 127.0.0.1
     database: circles_production
     username: circles
     password: circles_p_roduction
   ```

**Model基础**

* 命名约定是驼峰式单数，数据库表名是蛇底式复数
* 新建一个model，默认继承于ActiveRecord::Base
* save（create or update）失败的原因：验证失败，或者数据库连接失败

**Model类方法**

* self.table_name = ""
* validates（from gem activemodel）
* scope
* associations
* before_save

**Scopes**

自定义查询范围

`scope :evils, -> { where(style: 'evil') }`

**Validation**

* 验证默认触发事件：save create update，可以用on参数指定触发事件
* 验证失败会把错误信息放进对象的errors中，如下方式可以在view中显示：`user.errors.messages.values.flatten.each`

* save之前看是否可以通过验证的方法：`user.valid?`
* 自定义validate，验证不通过使用`errors.add(:name, 'info')`，return boolean
* 跳过验证：save(validate: false) update_attribute/column(s)

**关联**

* 描述表间关系
* 分类：
  1. 自关联
  2. 一对多
  3. 一对一
  4. 多对多：需要手动建立关联关系表
     * has_and_belongs_to_many，无需访问中间表
     * has_many :through，需要访问中间表及其中自定义字段

* blog belongs_to :user ->blogs中需要有user_id外键
* 参数：
  * -> { where } 
  * class_name
  * foreign_key
  * primary_key
  * dependent: :destroy

**Callback**

* 与对象的生命周期<u>增删改查</u>的事件绑定的方法

* <u>会触发callback的方法与不会触发callback的方法</u>
* 所有的回调被自动放在事务中，<u>before回调</u>返回false就会rollback，<u>after回调</u>需要抛出异常才会rollback

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvil5x5s1zj30u30h1q6g.jpg)

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvilpeqsjvj30tn0gc77r.jpg)

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvilp5jvmbj30tk09v40m.jpg)

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvilpklzt7j30t6079q3t.jpg)

**CRUD深入**

<u>查询</u>

* find vs find_by vs find_by！ 
  * find参数是i，找不到会异常
  * find_by参数是属性键值对，找不到返回nil
  * find_by！找不到会异常

* find_by_sql 返回是数组，使用场景：复杂的需要定制的查询。需要注意sql注入风险。
* where：n+1查询：
  * 表现：Blog.where().comments
  * 解决办法：Blog.where().<u>includes(:comments)</u>.comments

<u>更新</u>

* new_record？是否new但是没保存到数据库
* changed? 是否有更改但是没保存到数据库
* changed_attributes 更改但是没保存到数据库的属性
* attributes = hash 之后需要save
* update_attributes 更新多个属性
* update_attribute 更新一个属性

**Model自定义属性**

* 无需表中有对应字段，或者可以覆盖表中已有字段
* 定义new_attr_name = 的方法，该方法表单中可以用来输入，c中白名单添加新属性
* 定义new_attr_name方法，编辑可以读取到已有的值

**Transaction**

* 事务是基于db的，而不是针对一个表
* 事务的作用是，一对操作一起成功或者一起失败

```ruby
Blog/blog.transaction do
	blog.save!
	raise 'error'
	# 有异常就会rollback,异常会继续往外层抛出
end
```

**Migrations**

* from gem activerecord
* 作用：
  1. 使用Ruby DSL管理数据库模式
  2. 通过RDB（关系型数据库）模式管理，在不同db之间使用
  3. 支持版本管理和团队协作
  4. 支持db rollback
  5. 不仅是操作数据库模式，还可以是对数据的修改

* rake -T 查看支持的rake db命令
* 永远不要修改已经提交的migrations，要不断新增并修改
* schema.rb映射数据库模式



## View

**View基础**

* gem：actionview（view层功能）& actionpack（controller层功能）
* 查找：app/views 以及自定义查找view目录append_view_path
* 分类：
  1. template：index.html.erb
  2. partial：_form.html.erb（使用的时候没有下划线）
  3. layout：application.html.erb

* 解析：完成ruby语法到字符串的解析
  * erb
    * <% %> 执行
    * <%= %>显示
    * <%# %> 注释
    * <%  -%> 结尾无换行
  * haml
  * builder：json builder，xml builder etc

* 命名：
  * index：action或partial的名字
  * html：输出文件类型（mime type），还可以是xml
  * erb：解释引擎

**引入Bootstrap**

* gemfile：`gem 'bootstrap-sass', '~>3.3.5.1'`
* app/assets/stylesheets/<u>application.scss</u>：
  * @import "bootstrap-sprockets";
  * @import "bootstrap";

**render & layout**

* render：
  * in controller：
    * 修改action的查找view的行为，构建http response
    * 可以返回各种格式（text json html xml file partial)
    * 支持的参数：
      * :content_type
      * :layout(false)
      * :location
      * :status(code & symbol)
  * in view：渲染partial`<%= render (partial:)"shared/navbar", locals: { var: @var } %>`
* layout：
  * 放公用的组件，例如：head、navbar、footer、flash以及自定义布局contend_for
  * controller中，`layout 'application'`显示指定使用的布局
  * 运行时选择逻辑，`layout :generate_layout`定义方法最终返回布局字符串

**helper**

* image_tag/image_path
* javascript_include_tag
* stylesheet_link_tag
* distance_of_time_in_words
* number_to_ (转换到不同领域的数字) number_with_（设置数字显示格式）
* strip_links (过滤用户输入内容防止被攻击,去掉链接) strip_tags(去掉标签)
* <% benchmark 'name' do %> ... <% end %> 可以log代码运行速度，VC中可用
* <% cache do %> ...<% end %>可以片段缓存，VC中可用
* <% var = capture do %>...html fragment...<% end %>，之后再<%= var%>
* <%= string.html_safe %> 才可以安全输出html标签，否则是纯字符串

**form_tag**

- 生成一个HTMLform，用于非模型相关的操作，比如：搜索、登陆
- 不同类型的input，都是以tag结尾：
  * text_field_tag
  * password_field_tag
  * email_field_tag
  * file_field_tag
  * submit_tag
  * etc

* 浏览器只支持GET/POST的请求方式，Rails的PATCH/PUT/DELETE请求实际上是通过POST请求方式然后在表单内部添加一个隐藏的输入，添加_method参数来区分，参数的值是put/patch/delete
* 方法参数：url（表单提交的地址），method: :post
* helper自动生成的两个隐藏的input：utf-8 & authenticity_token
* input标签最重要的是name属性，与参数传递的key有关，id属性是根据name属性生成的

**form_for**

- 生成一个HTMLform，用于模型相关的操作，比如：商品创建
- 不同类型的input，调用方式f.helper，第一个参数必须为实例拥有的属性：
  * text_field
  * password_field
  * email_field
  * file_field
  * submit
  * fields_for(为其他模型的属性输入)
  * etc

* 和资源实例绑定，方法参数为实例变量@+url+hash
* 支持模型关联，一对一、一对多
* 自动表单数据设置，提交失败会保留错误内容，机制：create创建失败render new而非redirect_to new，使用的是create的@user，而不是new中的@new
* 支持表单参数传递命名空间，params.require(:user).permit(:name)，有不允许的参数输入会异常

**CSRF攻击与防治**

* 原因：允许用户自定义html tag（img的src属性以及a的onclick属性），而且后端没有过滤直接输出

* Rails的应对措施：

  1. 使用正确的HTTP请求方式

  2. application controller中，protect_form_forgery

  3. 表单的authenticity_token参数，只有后端知道token，匹配一致才是合法请求

  4. 对于ajax请求，csrf_meta_tags提供authenticity_token。

  5. Rails防范不了js取得token再发起请求的攻击，需要网站配合：

     * 后端需要在存入输入时需要过滤以及替换非法标签以及字符串

     * 输出时，只允许白名单中的标签输出，其他都不输出，escape掉

**Rails Ajax**

<u>Jquery Ajax</u>

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvi8exq1rfj30tv0e3776.jpg)

<u>Ajax in Rails</u>

* gem 'jquery_rails' 把jquery整合进helper
* application.js：require jquery以及jquery_ujs
* 使用：form_for也是添加remote: true参数
* action需要render json: { }

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvi8k1rnf6j30u40c741m.jpg)

<u>Ajax不支持文件上传</u>

* ajax本质时js，js不支持文件操作
* 异步上传文件：实质是同步，需要采用iframe的形式，设置表单target为frame
* 或者采用js第三方组件，例如AjaxUpload，原理同上

## Controller

**Controller基础**

* 来源于gem actionpack
* 命名约定是复数，users_controller.rb，对应文件中UsersController
* 新建一个controller，默认继承于ApplicationController，又继承于ActionController::Base
* action中通过实例变量@向对应的view传递数据
* action中通过params获取view中的表单向controller传递的参数
* 一个action内部只能有一个redirect_to或者render
* action内部使用return关键字跳出action执行
* 默认的以及自定义的action都需要以redirect_to或者render结束，否则会提示找不到模版的异常
* app/controllers/concerns可以放一些通用的逻辑，封装成模块

**redirect_to**

* redirect_to：
  * 重新发起一次请求 
  * redirect_to url/:back
  * 参数：:status
  * vs render:
    * 相同点：都是完成http响应
    * 不同点：render是构建http响应，redirect_to是重新发起请求直到拿到响应

**session vs cookies**

* 背景：HTTP 短连接（请求响应之后连接就会被切断）、无状态
* HTTP用cookies保存状态：
  * 服务器到浏览器的响应：set-cookie
  * 浏览器到服务器的请求：cookie
* session是后端实现，cookie是浏览器实现
* session的key存储基于cookie，也可以基于后端的数据库存储
* session支持存储后端的数据结构，例如对象；cookie只支持字符串
* 两者的读取以及写入形式都如同哈希
* session没有大小限制；cookie有大小限制
* cookie设置有效期（默认是会话结束）以及域名（作用域）信息
* config/initializers/session_store.rb中设置session存储方式以及存储于cookie指定的key

**controller中其他方法**

* send_file、send_data返回响应结果
* request
  * request.fullpath
  * request.get?
  * request.ip
  * request.headers
  * request.body
* response
  * response.location
  * response.response_code
  * response.body=

* Rails.logger.info "debug_info" 手动添加日志调试信息

**controller中的类方法**

* before/around/after_filter：在action之前/前后/之后运行的方法
* protect_from_forgery
* helper_method：定义c对应的v中可以用的helper，例如：current_user
* rescue_from 异常类, with: :method，方法定义抓取异常后的处理

**current_user设计**

```ruby
module Concerns
  module UserSession
    def self.included base
      base.class_eval do
        helper_method :logged_in?
        helper_method :current_user
      end
    end

    def signin_user user
      session[:user_id] = user.id
    end

    def logout_user
      session[:user_id] = nil
    end

    def logged_in?
      !!session[:user_id]
    end

    def current_user
      if logged_in?
        @current_user ||= User.find(session[:user_id])
      else
        nil    
      end
    end
  end
end
```

## Route

**Route基础**

* 规定了特定格式的URL请求到后端controller的action的分发规则
* config/routes.rb
* 使用指定方法和参数来生成路由
* 路由查找是自上而下的，前面的会覆盖后面的路由
* 可以对路由的片段做限制（正则表达式）
* 可以重定向 to: redirect(url)

**路由设计**

**普通路由 **

```ruby
get 'users/:id', to: 'users#show'
#same as
get 'users/:id' => 'users#show'
#action params[:id]

match 'photos', to: 'photos#show', via: [:get, :post]
```

**命名路由**

```ruby
#定义 生成helper
get 'users/:id', to: 'users#show', as: 'user'
#使用
user_path/url(@user)
user_path/url(@user.id)
```

**复数资源**：同一个URL可以拥有不同的请求方式（GET/POST/PUT/DELETE）

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvcvdbmpmaj30tv0eu7b2.jpg)

rake routes 查看helper方法、http verb、url pattern以及controller#action

**单数资源**

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fvcvtfmjmaj30tp0goq8i.jpg)

**命名空间**

代码的命名空间和url的前缀两者都有

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvcvvuijczj30uc0hstej.jpg)

**scope**

代码的命名空间和url的前缀两者只选择其中之一

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fvcvxydw02j30tc0ap0vi.jpg)

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvcvz4vus5j30tc0iiahr.jpg)

**嵌套路由**

嵌套路由最多一共两层

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvcw3iryj7j30t50imag5.jpg)

**部分的action**

```ruby
resources :users, only/except: [:index, :destroy]
```

**添加自定义的路由**

* member：需要传递ID
* collection：不需要传递ID

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fvcwlzhqp1j30tp0g2tcs.jpg)

**根路由**

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fvcwyq1qqfj30tz09k40b.jpg)

