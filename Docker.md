## Docker



### 是什么

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口，更重要的是容器性能开销极低。



### 架构

![img](https://7n.w3cschool.cn/attachments/image/576507-docker1.png)



| Docker 镜像(Images)    | Docker 镜像是用于创建 Docker 容器的模板。                    |
| ---------------------- | ------------------------------------------------------------ |
| Docker 容器(Container) | 容器是独立运行的一个或一组应用。                             |
| Docker 客户端(Client)  | Docker 客户端通过命令行或者其他工具使用 Docker API与 Docker 的守护进程通信。 |
| Docker 主机(Host)      | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。       |
| Docker 仓库(Registry)  | Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。Docker Hub 提供了庞大的镜像集合供使用。 |
| Docker Machine         | Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker，比如VirtualBox、 Digital Ocean、Microsoft Azure |

**Docker 容器通过 Docker 镜像来创建。容器与镜像的关系类似于面向对象编程中的对象与类。**



### 应用

- Web 应用的自动化打包和发布。
- 自动化测试和持续集成、发布。
- 在服务型环境中部署和调整数据库或其他的后台应用。
- 从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。



### 使用

#### 容器

**创建并运行**

1. 直接运行容器

```bash
docker run ubuntu:15.10 /bin/echo "Hello world"
```

2. 运行交互式(bash)的容器

```shell
docker run -i -t ubuntu:15.10 /bin/bash
```

3. 后台运行容器

```shell
docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

选项：

- -d：在后台运行

- -P：自动端口映射

- -p：指定端口映射

  - -p 5000:5000

  - -p 127.0.0.1:5000:5000
  - -p 127.0.0.1:5000:5000/udp

- -i：与容器交互

- -t：指定容器的终端

- --name：指定容器名称 --name custom_name

**查看容器列表**

- 运行的容器：docker ps 
- 所有容器：docker ps -a

**查看容器输出内容**

docker logs container_id/container_name

**查看容器端口映射**

docker port container_id/container_name

**查看容器内部运行的进程**

docker top id/name

**查看容器的配置和状态信息**

docker inspect id/name

**删除/停止/启动/重启容器**

docker rm/stop/start/restart 2b1b7a428627/thirsty_monkey

**进入运行中的容器的 shell**

docker attach id/name

docker exec -i -t  id/name /bin/bash

**导出容器快照**

docker export id/name > ubuntu.tar

**导入容器快照为镜像**

cat ubuntu.tar | sudo docker import - test/ubuntu:v1.0



#### 镜像

**列出本地所有镜像**

docker images

**创建镜像**

1. 编写Dockerfile(一组指令来告诉 Docker 如何构建我们的镜像)
2. docker build --rm=true -t youj/centos:6.7 . (-t 为指定目标镜像名 .为 dockerfile 所在文件夹路径)

**拓展更新镜像**

1. 使用原镜像创建一个容器
2. 更新并退出容器
3. 从容器提交新镜像：docker commit -m="has update" -a="youj" e218edb10161 w3cschool/ubuntu:v2

**删除镜像**

docker rmi training/sinatra

**为镜像添加新标签**

docker tag 860c279d2fec youj/centos:dev

**保存镜像**

docker save myimage | bzip2 -9 -c> /home/save.tar.bz2

docker save -o ubuntu_14.04.tar ubuntu:14.04

**加载镜像**

bzip2 -d -c < /home/save.tar.bz2 | docker load

docker load --input ubuntu_14.04.tar  or  docker load < ubuntu_14.04.tar



#### 仓库

**CLI 登录**

docker login

**搜索镜像**

docker search httpd

**获取镜像**

docker pull ubuntu:13.10

**推送镜像**

docker push ouruser/sinatra

**自动创建**

实质：当监听的分支产生 git push 时，自动执行Dockerfile，更新镜像

**私有仓库**

docker-registry安装本地私有仓库



#### 其他

**查看 docker 所有命令**

docker

**查看具体一条命令的使用方法**

docker COMMAND --help



### 数据管理

**数据卷**

利用数据卷对其中的数据进行进行备份、恢复和迁移

- 数据卷可以在容器之间共享和重用
- 对数据卷的修改会立马生效
- 对数据卷的更新，不会影响镜像
- 卷会一直存在，直到没有容器使用

**数据卷容器**

实质就是一个正常的容器，专门用来提供数据卷供其它容器挂载的。这样，可以有一些持续更新的数据在容器之间共享。

#### **应用**

**创建数据卷**

docker run + -v 选项

- 只在容器内部：docker run -d -P --name web -v /webapp training/webapp python app.py OR Dockerfile VOLUME
- 主机目录映射容器目录：docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py

**数据备份**

- 创建数据卷容器：docker run -d -v /dbdata --name dbdata training/postgres echo Data-only container for postgres
- 创建挂在数据卷容器中数据卷的容器，映射到主机目录，并备份数据：docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata

**数据恢复**

- 创建数据卷容器：docker run -v /dbdata --name dbdata2 ubuntu /bin/bash
- 创建挂在数据卷容器中的数据卷的容器，映射到主机目录，并恢复数据：docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar -C /data



### 容器网络

**外部访问容器/端口映射**

docker run -P /-p 

**容器互联**

该系统会在源和接收容器之间创建一个隧道，接收容器可以看到源容器指定的信息。

1. 创建子容器：docker run -d --name db training/postgres
2. 创建父容器并连接：docker run -d -P --name web --link db:db training/webapp python app.py
3. 子容器查看连接信息：docker ps -> NAMES -> db,web/db
4. 父容器查看连接信息：进入父容器内，env或者 cat /etc/hosts

**容器网络配置**

配置方式：服务启动时配置or 容器启动时配置

配置内容：...



### Dockerfile

Dockerfile 分为四部分：

1. 基础镜像信息：FROM
2. 维护者信息：MAINTAINER
3. 升级镜像指令：RUN
4. 容器启动时执行指令：CMD。
   - 每个 Dockerfile 只能有一条 `CMD` 命令
   - 如果用户启动容器时候指定了运行的命令，则会覆盖掉 `CMD` 指定的命令。

其他：

- #：注释
- ENV：设定环境变量PG_MAJOR，在 RUN 中使用($PG_MAJOR)
- EXPOSE：向主机开放的端口
- ADD：复制本地目录/URL/tar到镜像目录中
- COPY：复制本地目录到镜像目录中
- ENTRYPOINT：容器启动后执行的命令
  - 每个 Dockerfile 中只能有一个 `ENTRYPOINT`
  - 不可被 `docker run` 提供的参数覆盖
- VOLUME：设置数据卷
- USER：运行容器时的用户名
- WORKDIR：为后续的 `RUN`、`CMD`、`ENTRYPOINT` 指令配置工作目录
- ONBUILD：当所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令



### Docker 底层实现

Docker 底层的核心技术包括

- Linux 上的名字空间（Namespaces）：每个容器都有自己单独的名字空间，运行在其中的应用都像是在独立的操作系统中运行一样。名字空间保证了容器之间彼此互不影响。
- 控制组（Control groups）：用来对共享资源如内存、CPU、磁盘 IO 进行隔离、限制、审计等。只有能控制分配到容器的资源，才能避免当多个容器同时运行时的对系统资源的竞争。
- Union 文件系统（Union file systems）:Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。另外，不同 Docker 容器就可以共享一些基础的文件系统层.
- 容器格式（Container format）：libcontainer格式

