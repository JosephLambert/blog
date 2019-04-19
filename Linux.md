## Linux

### 远程登录

- IP+用户名+密码
- openssh免密登录



### Linux命令分类

- 文件管理
- 文件编辑
- 文件传输
- 磁盘管理
- 磁盘维护
- 网络通讯
- 系统管理
- 系统设置
- 备份压缩
- 设备管理

参考链接 ：[Linux 命令大全](https://www.w3cschool.cn/linux/linux-command-manual.html)



`man COMMAND` 查看一条指令COMMAND的使用文档



### 文件属性

显示一个文件的属性以及文件所属的用户和组:：ll 或者 ls –l 

返回结果实例：`dr-xr-xr-x   2 root root 4096 Dec 14  2012 bin`

**文件属性**

![363003_1227493859FdXT](https://7n.w3cschool.cn/attachments/uploads/2014/06/363003_1227493859FdXT.png)



**操作**

- chgrp：更改文件属组
- chown：更改文件属主，同时也可更改文件属组
- chmod：更改文件的9 个属性



### 文件和目录管理

**目录管理**

- ls: 列出目录
- cd：切换目录
- pwd：显示目前的目录
- mkdir：创建一个新的目录
- rmdir：删除一个空的目录
- cp: 复制文件或目录
- rm: 移除文件或目录
- mv: 移动文件与目录、文件重命名

**文件内容查看**

- cat  由第一行开始显示文件内容
- tac  从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
- nl   显示的时候，顺道输出行号！
- more 一页一页的显示文件内容
- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
- head 只看头几行
- tail 只看尾巴几行



### 用户和用户组管理