## Shell

### 是什么

1. Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问Linux操作系统内核的服务，是用户使用Linux的桥梁。
2. Shell 是一种编写脚本程序的语言。



### 创建shell脚本

**test.sh**

```shell
#!/bin/bash 
echo "Hello World !"
```

"#!" 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行

改变第一行为：#!/usr/bin/env ruby，可以改为 ruby 语言来写。

另外，文件后缀无所谓 sh 或者 rb。



### 运行脚本

**方式 1：作为可执行程序**

1. chmod +x ./test.sh  #使脚本具有执行权限，之后文件会变成红色
2. ./test.sh  #执行脚本

注意：一定要写成./test.sh，而不是test.sh，运行其它二进制的程序也一样，直接写test.sh，linux系统会去PATH里寻找有没有叫test.sh的，而只有/bin, /sbin, /usr/bin，/usr/sbin等在PATH里，你的当前目录通常不在PATH里，所以写成test.sh是会找不到命令的，要用./test.sh告诉系统说，就在当前目录找。

**方式 2：作为解释器参数**

- /bin/sh test.sh
- ruby test.rb

注意：这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。



### 变量

**定义或(二次)赋值**

`name='youzi'`

**使用**

1. `echo $name`
2. `echo ${name}`



### 常用数据类型

**字符串**

- 单双引号区别同 ruby

**数组**

- 定义：array_name=(value0 value1 value2 value3)
- 读：valuen=${array_name[n]} 
- 写：array_name[1]=value1
- 不支持多维数组
- array_name[@] 或 array_name[*] 表示 数组的所有元素
- 取得数组元素的个数：length=${#array_name[@]}

**注释**

- #开头单行注释
- 没有多行注释，需要的话用{}围起来，定义为一个函数，没有地方调用，效果一样



### 参数传递

**传入参数**

./test.sh 1 2 3 4

**脚本内读取参数**

- 1 2 3 4 算参数，通过$n读取
- $0 读取文件名
- $* $@ 一次读取所有参数，分别称为一个变量和 n 个变量
- $# 参数个数
- $$脚本当前进程 ID



### 运算符

- 算数运算符：加减乘除
- 关系运算符：大小对比，作为 if 的条件使用
- 布尔运算符：与或非，作为 if 的条件使用
- 字符串运算符：判断字符串是否为空或者字符串之间是否相等，作为 if 的条件使用
- 文件测试运算符：判断文件属性，作为 if 的条件使用



### 流程控制

**条件判断**

if

```shell
if condition1
then
    command1
elif condition2
    command2
else
    commandN
fi
```

case

```shell
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```

**循环**

for 循环

```shell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

while 循环

```shell
while condition
do
    command
done
```



### 函数

**定义与使用**

```shell
#!/bin/bash
demoFun(){
    echo "这是我的第一个 shell 函数!"
}
echo "-----函数开始执行-----"
demoFun
echo "-----函数执行完毕-----"
```



### 输入输出重定向

一个命令通常从一个叫标准输入stdin的地方读取输入，默认情况下，这恰好是你的终端。

同样，一个命令通常将其输出写入到标准输出stdout，默认情况下，这也是你的终端。

你可以改变读取输入或者写入输出的地方。

| 命令            | 说明                              |
| :-------------- | :-------------------------------- |
| command > file  | 将输出重定向到 file。             |
| command < file  | 将输入重定向到 file。             |
| command >> file | 将输出以追加的方式重定向到 file。 |

禁止输出

command > /dev/null



### 文件包含

```shell
. filename   # 注意点号(.)和文件名中间有一空格
或
source filename
```



### 常用命令

- echo 输出字符串
- printf格式化实处字符串
- expr 表达式求值计算
- 反引号执行命令 echo \`expr 1 + 2\`
- test 检测条件是否成立：作为 if 的条件使用
- read 与用户交互，读取用户接下来的输入
- export 设置或显示环境变量
- alias 设置别名