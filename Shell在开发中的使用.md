### Shell在开发中的使用

**查看shell类型**

`$ echo $SHELL` -> bin/bash 默认为bash，还有一种zsh

**拓展工具**

- bash：[bash-it](https://github.com/Bash-it/bash-it)
- zsh：[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

#### 日常文件操作

**查看文件**

- ls 查看文件
- ls -a 查看所有文件，包含隐藏文件（以.开头）
- ls -l 列表形式查看文件（权限、作者、大小、修改时间、文件名）
- ls -lh 列表形式查看文件，文件大小人可读性高
- ls -lhS 列表形式查看文件，文件大小人可读性高，并按照文件大小从大到小排序

**进入文件夹**

- cd some_dir/
- cd .. 进入上层文件夹

**复制文件**

- cp a b 将a复制到b
- cp -rf r代表递归包含文件夹里的文件，f代表强制

**移动或重命名**

- mv a.rb b.rb 将a重命名为b 等价于 rename命令
- mv a.rb keep/ 将a移动到keep文件夹里

**打印文本内容**

- cat a.rb 打印整个文本
- head -n 10 test.log 打印test.log文件的前10行
- tail -n 10 test.log 打印test.log文件的最后10行
- tail -f test.log 打印test.log最新更新的文本
- less test.log 只打印一个屏幕的内容，可以用回车（往下滚动）以及空格（另一个屏幕）继续打印，等价于cat test.log | less

**过滤文本**

- grep ruby a.txt 过滤出a.txt中包含ruby的行 等价于 cat a.txt | grep ruby
- grep -i ruby a.txt 增加了忽略大小写的功能 等价于 cat a.txt | grep -i ruby
- grep -v ruby a.txt 过滤出a.txt中不包含ruby的行 等价于 cat a.txt | grep -v ruby
- grep -n ruby a.txt 过滤出a.txt中包含ruby的行以及行数 等价于 cat a.txt | grep -n ruby
- tail -f test.log | grep -B 10 -A 10 'exception' 匹配最新内容中exception（regexp）的上下文各10行
- fgrep ruby a.txt 与grep的区别是fgrep只支持字符串匹配不支持正则表达式，性能更高
- egrep ruby a.txt  与grep的区别是egrep不仅支持grep的shell中简单正则表达式，还支持传统编程语言中更复杂的正则表达式

#### Shell的管理和快捷键

**上下箭头查看之前执行过的命令**

echo $HISTSIZE查看默认保存命令数量为500。

如果想要修改：

1.   vi ~/.profile
2. 添加内容 export HISTSIZE=1000
3. ESC :wq保存退出
4. source ~/.profile调用文件使得改动立即生效
5. echo $HISTSIZE 查看改动结果

**查看之前执行过的某类命令**

-   ctrl+R后进入搜索模式，输入该类命令的前几个单词，弹出最近的复合匹配的命令，接着crtl+R网上查找，直到找到后按下回车。

- 输入该类命令的前几个单词后+上下键

**快速修改输入的长命令**

- ctrl+w 从后往前删除一个单词
- ctrl+u 从后往前删除所有单词
- ctrl+d 从前往后删除一个字母
- ctrl+k 从前往后删除光标后的所有字母
- ctrl+a 光标跳至最开始
- ctrl+e 光标跳至最末尾

**清空屏幕**

- clear 或者 ctrl+l 使得输入光标位置至最顶部
- cmd+k 从内存清空shell

#### 进程控制

- ctrl+c 退出正在运行的进程
- ctrl+z 暂停挂起进程，之后
  - kill  %1终止该进程；kill -9 %1从操作系统层面杀死该进程（不建议）
  - fg 继续运行该进程

- rails  s  & 放至后台运行进程，同时命令行也是可以使用的，fg放至前台
- kill命令是与进程通信
  - kill -l 查看可以与进程通信的所有命令
  - kill -9 process_id ; kill -KILL process_id
  - kill 直接加process_id，默认是-15 也就是 -TREM

- ps 查看当前shell中正在运行的进程
- ps aux 查看操作系统中所有正在运行的进程

#### 网络调试和工具

- ifconfig 查看电脑网卡的IP地址，其中en0
- netstat -an 查看网络连接状态，加 | grep LISTEN 查看开启的端口号
- dig baidu.com 查看域名相关的IP信息，dig baidu.com mx 查看域名相关的邮箱信息
- curl 通过命令行发起HTTP请求的工具 
  1. curl ifconfig.me 可以得到本机公网IP
  2. curl www.baidu.com 请求网站
  3. curl -L www.baidu.com 遇到重定向的网站也继续请求
  4. curl -i www.baidu.com 附加显示响应的头部信息
  5. curl -v www.baidu.com 附加显示请求和响应的头部信息
  6. curl -H "a=1" www.baidu.com 定制头部信息
  7. curl -F "a=1" www.baidu.com提交表单参数，默认post请求
  8. curl -X POST www.badu.com 指定请求类型

- wget 命令行下载工具

#### 系统管理

- w 查看电脑开启时间、负载，等价于uptime（更简洁）
- date 查看时间
- du -h keep/ 查看某个文件夹或者文件的大小以可读的方式
- df -h 查看磁盘使用情况
- chown new_user:new_group a.txt 修改a.txt的所属用户为new_user，所属用户组为new_group
- chmod u=rwx,g+rw,o-x a.txt 修改文件拥有者、所属用户组、其他人的读写执行的权限，另一种写法 chmod 755 a.txt
- sudo 加命令 以root身份运行没有权限的命令

#### 自定义命令

- 可以将重复的流程化的命令打包为一条自定义的命令执行

1. vi ~/.profile

2. i 输入

   ```shell
   railss() {
   	# 可以传递参数 $1为第一个参数
       cd some/path
       rails s -p 3001
   }
   ```

3. 保存退出

4. 新打开窗口即可执行railss命令

- 设置命令的别名

  `alias l="ls -l"`

- 设置自定义可执行脚本

  1. 在～/.profile将某个路径放进$PATH，这样在shell中输入命令，会在该路径下搜索可执行文件并执行。

     ```shell
     export PATH=some/dir/path:$PATH
     ```

  2. 在该目录下编写文件 test_cmd

     ```shell
     #!/usr/bin/env ruby
        (上面的注释使得之后的代码可以用ruby编写)
     # -*- coding: UTF-8 -*-
        (上面的注释使得可以使用中文编码)
   puts test cmd test cmd
     ```
  
  3.  chmod +x test_cmd 使得该文件可以被执行
  4. 在新打开的窗口执行test_cmd命令即可