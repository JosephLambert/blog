# Javascript编程入门-JavaScript语言核心

**程序的核心**

处理数据得到结果

# 一切从命名开始

**命名的作用**

把数据赋值给变量，通过变量来操作数据。

**命名方式**

变量先声明后定义

* 方式一：`var output = 'hello,js'；`
* 方式二：`var output;` `output = 'hello,js';`

**为变量起名**

* 驼峰式：`myLikeColorList`
* 蛇底式：`my_like_color_list`

**好的命名原则**

* 完整代表了数据的含义，名字包含更多的信息![](https://ws2.sinaimg.cn/large/006tNbRwgy1fum249gakwj30m90gsmzx.jpg)
* 反映了问题而非解决问题，要what不是how：坏的命名：`var inputData;` 好的命名：`var employeeData`

# 数据交互

**浏览器和服务器的交互**

1. 获取html文件
2. 解析html文件
3. 获取css/js
4. 更新样式/执行js代码
5. 我要数据
6. 给你数据

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fumv3dsk76j30op0iqwh9.jpg)

**JS在前端的作用**

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fumv55f6o9j30ov0ip41f.jpg)

**前端交互实现关注点**

1. 用户和html的交互
2. html和js的交互
3. js和服务器的交互

**数据类型**

1. 数字
2. 字符串
3. 布尔
4. 数组
5. 对象（同ruby中的哈希）

**数据类型的价值**

不同的数据类型决定了代码的效率：存储效率，计算效率，传递效率

**注意**

定义了的变量一定要使用，否则就是垃圾代码，白占内存

**三大语句结构**

1. 顺序

2. 条件  

   ``` javascript
   if (age>=18) {
       console.log('已成年');
   } else {
       console.log('未成年');
   }
   ```

3. 循环

   ``` javascript
   for (var i = 0; i < myLikeFoodList.length; i++) {
       console.log(myLikeFoodList[i]);
   }
   ```


# 函数

**函数是什么**

函数黑盒（蓝色部分）的作用：

1. 参数判断：增加如果参数为空或者参数类型错误的逻辑判断，增强兼容性
2. 逻辑处理
3. 返回结果

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fumv6b6ykjj30om0im761.jpg)

**函数定义与调用**

定义：不执行代码，只是放在内存等待调用

调用：执行代码

``` javascript
function counter(start_number) {
    end_number = start_number + 1;
    return end_number;
}
var start = 1;
var end = counter(start);
```

**函数返回-同步-立即返回**

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fumv9icbhej30ol0iiwgb.jpg)

立即返回示意图翻译为代码

``` javascript
function checkFlightSchedule() {
  console.log('开始查航班信息');

  // 模拟构建航班的时间信息
  var scheduleInfo = {
    start: '8/23 8:00',
    end: '8/23 10:30',
    from: 'beijing',
    to: 'hangzhou'
  };

  return scheduleInfo.start;
}

//---------------------------------------

// 调用立即函数checkFlightSchedule
var startTime = checkFlightSchedule();
console.log(startTime);
```

**函数返回-同步-间接返回(回调)**

回调又称为钩子函数，就像挂钩子，定义一个执行到某个地方时触发的方法，然后挂上去。

回调是由函数定义者定义的，而不是函数调用者。

![callback参数推理](https://ws4.sinaimg.cn/large/006tNbRwgy1funxv6mdacj30p40hsh16.jpg)

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fumvhueequj30oo0in76e.jpg)

间接返回示意图翻译为代码

```javascript
// checkFlightSchedule函数的定义者设计了一个通过回调给结果的方案。
// 当函数处理完后，通过调用者设置过来的回调执行输出
function checkFlightSchedule(callback) {
  console.log('开始查航班信息');

  // 模拟构建航班的时间信息
  var scheduleInfo = {
    start: '8/23 8:00',
    end: '8/23 10:30',
    from: 'beijing',
    to: 'hangzhou'
  };

  callback(scheduleInfo.start);
}

//---------------------------------------
function callback(startTime) {
  console.log(startTime);
}

// 调用函数checkFlightSchedule，并塞一个回调函数以获取结果
checkFlightSchedule(callback);
```

**函数返回-异步-间接返回**

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fumvi1tzmwj30ot0inaby.jpg)

执行顺序：先同步后异步

异步示意图翻译为代码

```javascript
function checkFlightSchedule(callback) {
  console.log('开始查航班信息');

  setTimeout(function() {
    // 模拟构建航班的时间信息
    var scheduleInfo = {
      start: '8/23 8:00',
      end: '8/23 10:30',
      from: 'beijing',
      to: 'hangzhou'
    };

    callback(scheduleInfo.start);
  }, 5000);
}

//---------------------------------------
function callback(startTime) {
  console.log('通过反馈通道获得了信息');
  console.log(startTime);
}

// 执行查询函数
checkFlightSchedule(callback);
```

 **NodeJS内置库示范-file system**

引入以及使用

```
var fs = require('fs');
var files = fs.readdirSync('./');
console.log(files);
```

**NodeJS第三方库示范-jsonfile**

1. 配置引入第三方库：Github搜索jsonfile，在他的package.json中寻找`name`以及`version`，写入`package.json`

2. ```json
   {
     "name": "useJSONFile",
     "dependencies": {
       "jsonfile": "3.0.1"
     }
   }
   ```

2. 安装：`npm install`
3. 代码库引入：`var jsonfile = require('jsonfile');`
4. 使用库函数：`jsonfile.readFile();`

**浏览器环境第三方库示范-Vue**

1. 引入库：`head`标签下的`script`中引入

2. 使用代码示范

   ```html
   <script>
     // vue官网： https://cn.vuejs.org/v2/guide/
     // 初始化Vue
     var obj = {
       el: '#abc',
       data: {
         title: '学习JavaScript'
       }
     };
     var app = new Vue(obj);
     setTimeout(function () {
       app.title = 'JavaScript真容易'
     }, 5000);
   </script>
   ```

   ```html
   <!-- 设置一个锚点，好让初始化vue的时候来依据这个锚点构建映射关系 -->
   <div id="abc">
     <!-- 引用-->
     <h1>{{ title }}</h1>
   </div>
   ```


**程序调试**

日志：`console.log(1);`，通过日志打印顺序反映代码执行顺序。