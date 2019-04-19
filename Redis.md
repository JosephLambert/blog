#### redis特点

- 高性能
- key-value形式存储
- 基于内存也可持久化存储到硬盘
- 支持主从同步模式的数据备份



#### redis安装并使用

**Mac平台**

```shell
$ wget http://download.redis.io/releases/redis-2.8.17.tar.gz
$ tar xzf redis-2.8.17.tar.gz
$ cd redis-2.8.17
$ make
```

**开启服务进程**

`$ redis-server`

**开启客户端程序redis-cli和服务器交互**

`$ redis-cli`



#### redis数据类型

- string：字符串
- hash：哈希
- list：按插入顺序排序的可重复数组
- set：无顺序的不可重复数组
- zset：可自定义顺序的不可重复数组

