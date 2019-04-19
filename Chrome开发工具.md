# Chrome开发者工具



## Elements-元素

- 编辑HTML
  - 审查元素
  - 编辑Dom
  - 设置Dom断点
  - 拖拽更改布局
  - 查看元素事件监听器
- 编辑CSS
  - 设置行内属性
  - 添加新选择器以及样式
  - 按照优先级编辑现有样式
  - 强制使用元素状态
  - 盒模型



## Console-控制台

- 记录诊断信息以及错误
- 与文档以及开发工具进行交互
- 控制台API（JS中输出信息到console或者console中直接执行）
  - console.log()
  - console.warn()orconsole.error()
  - console.assert()
  - console.group()以及console.groupEnd()
  - console.table()
  - console.dir()
  - console.time()以及console.timeEnd()
  - console.count()
- 命令行API
  - 计算表达式
  - $()
  - $$()
  - $x()
  - $_
  - inspect()
  - $0-$4
  - monitorEvents()监听事件
  - profile()开启JS CPU检测
- 选择上下文框架
- 新页面时保留日志
- 记录XHR请求



## Sources-源

- 实时调试Javascript
  - 控制执行
  - 观察表达式
  - 堆栈调用
  - 变量
  - 设置断点
  - XHR断点
  - Dom断点
  - 事件监听器断点
- Filesystem/Workspace通过映射保存更改
- Snippets（JS片段）
- 查看本地修改（修改的历史记录）以及撤销修改
- 格式化代码
- 发生异常时暂停
- 源映射
  - coffee-js
  - sass-css
- 该面板中**ESC**打开**console**



## Network-网络

- 加载资源的详细时序数据（waterfall）以及其他信息
- 查看资源的详细信息
  - 头
  - 预览
  - 响应
  - waterfall
- 排序和过滤
- 清除缓存以及cookies
- 记录并分析追踪栈
- 导出HAR
- 复制为curl
- large resource rows



## Performance-性能

提供加载网页过程中关于时间开销的完整概述

- 事件花费的时间
  - loading events
  - scripting
  - rendering
  - painting
- 帧渲染情况
- 内存占用情况
- 可以保存导出以及导入



## Memory-内存

- JS内存使用情况分析
- 堆配置视图



## Application-应用

检查应用程序的本地资源

- Frames页面资源
- IndexedDB
- Web SQL
- Local Storage
- Session Storage
- Cookies
- Application Cache



## Audits-审计

分析一个页面并提供优化建议



## 其他功能

- `Cmd`+`Shift`+`C`进入检查元素模式
- 设备模式
  - 选择预定义以及自定义设备
  - 模拟屏幕分辨率
  - 更改方向
  - 设备像素比
  - 网络模拟器
  - 审查CSS媒体
- 更改调试工具栏Dock的位置
- 安卓远程调试
  - 在电脑上调试手机中浏览器选项卡
  - 在电脑上调试手机中应用的WebView
  - 将手机屏幕从手机投影到电脑上
  - 使用端口转发以及虚拟主机映射来让安卓设备访问开发使用的服务器
- Sensor
  - 模拟触发事件
  - 模拟地理位置信息
  - 模拟设备朝向
- Chrome任务管理器
- Devtools拓展：RailsPanel



## 快捷键

- 打开开发者工具：Cmd + Opt + I
- 打开控制台：Cmd + Opt + J
- 打开设置：f1 or ？
- 下一个面板：Cmd + ]
- 上一个面板：Cmd + [
- 元素检查模式：Cmd + Shift + C
- 切换devtool的位置：Cmd+ Shift + D
- 设备模式：Cmd + Shift + M
- 刷新页面：Cmd + R
- 忽略缓存内容刷新页面：Cmd + Shift + R
- 在选中文件或者面板中进行文字搜索：Cmd +F
- 查找下一个：Cmd + G
- 查找前一个：Cmd + Shift + G
- 在所有源中进行文字搜索：Cmd + Opt + F
- 根据文件名搜索：Cmd + O
- 缩放devtool：Shift +  + or - or 0
- 开始记录：Cmd + E

**Elements**

- 展开所有子节点：Opt + Click on arrow icon
- 隐藏元素：H
- 转至样式规则属性声明中源行：Cmd + 点击属性
- 后一个/前一个属性/属性：Tab ,Shift + Tab

**Sources**

- 跳转到行：Ctrl + G
- 搜索函数：Cmd + Shift +O
- 选择下一个：Cmd + D
- 撤销最后的选择：Cmd + U

**Console**

- 清除控制台：Cmd + K , Opt + L
- 多行输入：Shift + Enter

**其他**

- 隐身模式打开新窗口：Cmd + Shift + N
- 切换书签栏开关：Cmd +Shift + B
- 查看历史页：Cmd + Y
- 查看下载页：Cmd +Shift + J
- 选中地址栏内容：Cmd + L