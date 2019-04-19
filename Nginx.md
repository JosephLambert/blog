## Nginx是什么

**概念**

Nginx是一个轻量级、高性能、稳定性高、并发性好的HTTP和反向代理服务器。

**框架**

![img](https://7n.w3cschool.cn/attachments/image/wk/nginx/chapter-2-1.png)

Master 进程：主要用来管理 worker 进程，包含：接收来自外界的信号，向各 worker 进程发送信号，监控 worker 进程的运行状态。

Worker 进程：基本的网络事件，则是放在 worker 进程中来处理了。多个 worker 进程之间是对等的，他们同等竞争来自客户端的请求，各进程互相之间是独立的。

**特性**

- 低内存高并发：异步非阻塞的方式来处理请求
- keep-alive：支持长连接。当客户端在发起请求前，需要先与服务端建立 TCP 连接，而每一次的 TCP 连接是需要三次握手来确定的。我们是可以在一个连接上面执行多个请求的，这就是所谓的长连接，但前提条件是我们先得确定请求头与响应体的长度。
- pipeline：pipeline 也是基于长连接的，目的就是利用一个连接做多次请求。如果客户端要提交多个请求，对于keepalive来说，那么第二个请求，必须要等到第一个请求的响应接收完全后，才能发起。Nginx 对 pipeline 中的多个请求的处理却不是并行的，依然是一个请求接一个请求的处理，只是在处理第一个请求的时候，客户端就可以发起第二个请求。
- lingering_close：当 Nginx 要关闭连接时，并非立即关闭连接，而是先关闭 tcp 连接的写，再等待一段时间后再关掉连接的读。lingering_close 的主要作用是保持更好的客户端兼容性，但是却需要消耗更多的额外资源。



## Nginx 能用来干什么

- 反向代理：代理服务器，代理服务器接受请求，然后将请求转发给内部网络的服务器(集群化)，并将从服务器上得到的结果返回给客户端，此时代理服务器对外就表现为一个服务器。可以提供灵活的功能，可以根据不同的正则采用不同的转发策略，不同的请求就可以走不同的服务器。
- 负载均衡：多在**高并发**情况下需要使用。其原理就是将数据流量分摊到多个服务器执行，减轻每台服务器的压力，多台服务器(集群)共同完成工作任务，从而提高了数据的吞吐量。Nginx可使用的负载均衡策略有：轮询（默认）、权重、ip_hash、url_hash(第三方)、fair(第三方)。
- 动静分离：Nginx提供的动静分离是指把动态请求和静态请求分离开，合适的服务器处理相应的请求，使整个服务器系统的性能、效率更高。Nginx可以根据配置对不同的请求做不同转发，这是动态分离的基础。静态请求对应的静态资源可以直接放在Nginx上做缓冲，更好的做法是放在相应的缓冲服务器上。动态请求由相应的后端服务器处理。
- 虚拟主机：有的网站访问量大，需要负载均衡。然而并不是所有网站都如此出色，有的网站，由于访问量太小，需要节省成本，将多个网站部署在同一台服务器上。例如将www.aaa.com和www.bbb.com两个网站部署在同一台服务器上，两个域名解析到同一个IP地址，但是用户通过两个域名却可以打开两个完全不同的网站，互相不影响，就像访问两个服务器一样，所以叫两个虚拟主机。
- 正向代理：代理客户端，科学上网就是一种正向代理。需要用户手动的设置代理服务器的ip和端口号。
- 静态HTTP服务器：Nginx是一个HTTP服务器，可以将服务器上的静态文件（如HTML、图片）通过HTTP协议展现给客户端。
- 本地配置反向代理域名：对比修改本地 hosts，可以隐藏端口号。



## Nginx 基本操作

```shell
启动服务：nginx
退出服务：nginx -s quit
强制关闭服务：nginx -s stop
重载服务：nginx -s reload　　（重载服务配置文件，类似于重启，但服务不会中止）
验证配置文件：nginx -t
使用配置文件：nginx -c "配置文件路径"
使用帮助：nginx -h
```



## Nginx配置

**Mac OS:**

Docroot目录（静态资源存放目录）: /usr/local/var/www or /usr/local/Cellar/nginx/1.15.11/html

配置文件目录：/usr/local/etc/nginx/nginx.conf

Nginx 会加载 /usr/local/etc/nginx/servers（自定义）/ 下的所有文件

开机自启动命令： brew services start nginx

启动服务命令：nginx



**配置系统**：

配置指令：

指令(单引号、双引号、无引号)+空格+参数

指令上下文：

- main: 全局配置，Nginx 在运行时与具体业务功能（比如http服务或者email服务代理）无关的一些参数，比如工作进程数，运行的身份等。
- events：工作模式配置
- http: 与提供 http 服务相关的一些配置参数。例如：是否使用 keepalive ，是否使用gzip进行压缩等。
- server: http 服务上支持若干虚拟主机。每个虚拟主机一个对应的 server 配置项，配置项里面包含该虚拟主机相关的配置。在提供 mail 服务的代理时，也可以建立若干 server，每个 server 通过监听的地址来区分。
- location: http 服务中的路由配置。
- mail: 实现 email 相关的 SMTP/IMAP/POP3 代理时，共享的一些配置项（因为可能实现多个代理，工作在多个监听地址上）。

每个上下文中都有具体的配置指令。

[Nginx 完整模块指令参考](https://nginx.org/en/docs/)



# 参考

- [Nginx正向代理与反向代理](<https://www.jianshu.com/p/ae76c223c6ef>) （含配置）
- [Nginx主要用来干什么](<https://blog.csdn.net/gyshun/article/details/81178233>)（含配置）
- [mac 下 nginx 配置多个虚拟主机](<https://blog.csdn.net/mengguihua110/article/details/79918612>)
- [nginx 配置虚拟主机的三种方法](<https://blog.csdn.net/baikeliang/article/details/72677687>)
- [mac下用nginx配置反向代理域名](<https://blog.csdn.net/zsnpromsie/article/details/79100377>)
- [MAC 配置域名 hosts](<https://blog.csdn.net/fanjunxi1990/article/details/21532655>)
- [Nginx 安装与部署配置以及Nginx和uWSGI开机自启](https://www.cnblogs.com/wcwnina/p/8728430.html)
- [Nginx 完整模块指令参考](https://nginx.org/en/docs/)