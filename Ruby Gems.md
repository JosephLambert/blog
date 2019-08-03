# Active Record Plugins



## Bit Fields

- flag_shih_tzu：将一个model 中的若干布尔型字段存储在一个整型字段中。
- bitmask_attribute：在一个整型字段中以 symbol 的形式存储若干可能的值。



## Default Values

- default_value_for：声明并设置 model 中一个字段的默认值



## Enumerations

- enumerize：在 model 中的一个string字段以 symbol 的形式存储若干可能的值。值通常为其中之一。



## Index Assistants

- foreigner：migration 中增加或删除外键依赖的方法

- lol_dba：扫描 model 并显示需要打索引的字段。生成 sql 脚本。

- schema_plus：拓展 AR 功能，打包 schema_plus家族的 gem。

- immigrant：generate 一个 migration，根据关联找到丢失的外键依赖。

  

## Named Scopes

- ransack：基于对象的搜索、过滤、排序



## Nesting

- ancestry：将 model 的对象组织成树形结构，并可以根据 scope 查询。



## Sharding

- ar-octopus：AR 数据库分片。



## Soft Delete

- paper_trail：对象的版本记录以及控制
- paranoia：软删除



## Sortables

- acts_as_list：管理一组对象的位置和重新排序 [sortable lists](http://railscasts.com/episodes/147-sortable-lists-revised)



## User Stamping

- userstamp：自动更新 created_by 以及 updated_by 属性
- [audited](https://github.com/collectiveidea/audited)：记录对象的所有变动



## Value Cleanup

- attribute_normalizer：使得写入数据规范化，使得数据一致性
- strip_attributes：验证前删除参数的首尾的空格，空变为 nil



## Versioning

- paper_trail：对象的版本记录以及控制
- [audited](https://github.com/collectiveidea/audited)：记录对象的所有变动



## Pagination

- kaminari & kaminari_bootstrap
- will_paginate & will_paginate-bootstrap
- ajax_pagination



## Rails Comments

- acts_as_commentable_with_threading：model 层的评论功能
- commontator：MVC 层的评论，包括邮件、投票、@功能



## Rails DB Bootstrapping

- seed-fu：种子数据
- populator：大量创建 model 的对象，其中数据可随机



## Rails Ratings

- ajaxful-rating：评分功能



## Rails Search

- elasticsearch-rails
- searchkick：基于 ES



## Rails Tagging

- acts_as_taggable_on：model 层的打标签、做标记



# Background Processing



## Background Jobs

- sidekiq：利用线程异步处理 定义好的Job



## Daemonizing

- daemons：控制 ruby 脚本在后台运行



## Daemon Management

- foreman：应用的多进程管理
- lunchy：打包 mac 的launchctl，可以启动、停止和管理守护进程、应用程序、进程和脚本等。



## Scheduling

- whenever：定时任务，可以运行方法、命令、rake 任务



# Code Organization



## Business Logic Frameworks

- trailblazer：对 Rails 做了新的抽象形成的高性能DDD架构。



## Form Objects

- virtus：为类和实例动态定义属性
- reform：trailblazer 的表单对象部分
- dry-validation:DSL 定义纲要和验证



## Service Objects

- active_interaction：封装商业逻辑
- mutations



## View Objects



