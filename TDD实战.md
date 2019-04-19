### TDD实战

**TDD概念**

测试驱动开发，先写测试用例，再写业务逻辑。

**测试的好处**

- 代码文档：是一种表明业务逻辑或者方法调用的文档
- 产品迭代：只需运行测试代码就可以知道是否影响到了之前的业务逻辑功能，从代码层面提供了自动化的保障
- 重构和升级：判断之前的逻辑功能是否依然能通过

**TDD的好处**

- 需求分析：先从功能以及需求的的角度分析产品
- 代码设计：提前整体规划代码

**技术**

- Ruby2.3.0
- Rails5.0.2
- gem RSpec（单元测试）
- gem Capybara（集成测试）

**安装并设置Rspec以及Capybara**

- gemflie添加两个gem
- 运行 $ bundle
- 运行 $ rails g rspec:install
- 集成测试 spec/features/visit_homepage_spec.rb

**TDD流程**

1. 分析需求编写测试用例
2. 运行测试 $ rake spec:features or $ rspec spec/features 
3. 根据错误提示编写业务逻辑代码
4. 不断循环2-3，直到返回1。如果需求发生改变，先修改测试文件。