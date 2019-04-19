## Ruby和Ruby on Rails开发环境安装

这篇文章给大家简单介绍下Ruby和Ruby on Rails环境的安装。

因为目前都是版本管理工具来安装和管理Ruby的。（即使你本地只想使用一个Ruby的版本，也建议采用版本管理工具来安装Ruby）

目前Ruby的版本管理工具主要就是rvm <https://rvm.io/> 和 rbenv <https://github.com/rbenv/rbenv>. rvm属于老牌的，rbenv相对新一点，我们这里采用rvm来给大家讲述，想使用rbenv的同学麻烦自行搜索。

以下安装步骤不论在Mac和Ubuntu上完全一致。

### 安装准备

首先保证你已经安装了curl工具，打开你的命令行（终端），执行下面的命令：

```
$ which curl
```

如果有内容输出就代表已经安装了curl，否则就需要先安装curl：

```
# Ubuntu
$ apt-get install curl

# Mac，如果你没有安装homebrew，请看这里 http://brew.sh/
$ brew install curl
```

### 安装rvm

```
$ curl -L get.rvm.io | bash -s stable
```

上面的命令会安装rvm，运行完毕之后你就可以使用rvm的命令了，如果没有，请关闭你的终端，重新打开一下，然后

### 安装ruby

```
$ rvm install 2.3.0
```

### 安装rails

```
$ gem install rails -v 5.0.0
```