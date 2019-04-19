# 内网穿透之HTTP穿透

更新时间: 2018-9-27

> 注意：鉴于很多开发者在临时体验开发时往往没有公网域名或者公网IP，本工具提供了一个公网代理服务，目的是方便开发测试。
> 本工具当前不保证多个开发者随意设置相同的子域名导致的冲突以及通道稳定性，因此正式应用、正式环境必须是真实的公网IP或者域名，正式应用上线绝对不能使用本工具。

# 内网穿透示意图

![image.png](https://gw.alipayobjects.com/zos/skylark-tools/public/files/de925671c3b6f1743f2db8ca229fc438.png)

# 场景介绍

案例一：ISV三方企业E应用的回调地址必须是公网域名或IP，对于大部分开发者来说，开发者无法在本地调试远程代码，对于回调URL校验不通过之类的问题无法追踪，只能不断远程部署查看log日志来调试修改。

案例二：企业通讯录同步过程中需要注册一个公网域名(或IP)的回调地址，用来接收钉钉上的企业通讯录信息变更。

内网穿透工具可以实现将一个公网域名映射到本地的localhost域名。

# 启动内网穿透

#### 1.下载工具

```plain
git clone https://github.com/open-dingtalk/pierced.git
```

![image](https://gw.alipayobjects.com/zos/skylark-tools/public/files/59fd4d93eaf4c0323df64cedaff08fb6.png)

启动工具，执行命令“./ding -config=./ding.cfg -subdomain=域名前缀 端口”，以mac为例：

```plain
cd mac_64
chmod 777 ./ding
./ding -config=./ding.cfg -subdomain=abcde 8080
```

启动后界面如下图所示：

![image](https://gw.alipayobjects.com/zos/skylark-tools/public/files/7552c06dd9a922602d13f56652940377.png)

命令参数说明：

| 参数       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| -config    | 内网穿透的配置文件，按命令照示例固定为钉钉提供的./ding.cfg，无需修改 |
| -subdomain | 您需要使用的域名前缀，该前缀将会匹配到“vaiwan.com”前面，例如你的subdomain是abcde，启动工具后会将abcde.vaiwan.com映射到本地。 |
| 端口       | 您需要代理的本地服务http-server端口，例如你本地端口为8080等  |

#### 2.启动完客户端后，你访问http://abcde.vaiwan.com/xxxxx都会映射到 <http://127.0.0.1:8080/xxxxx>

注意：

> 1.你需要访问的域名是http://abcde.vaiwan.com/xxxxx 而不是http://abcde.vaiwan.com:8082/xxxxx
> 2.你启动命令的subdomain参数有可能被别人占用，尽量不要用常用字符，可以用自己公司名的拼音，例如：alibaba、dingding等。
> 3.可以在本地起个http-server服务，放置一个index.html文件，然后访问http://abcde.vaiwan.com/index.html测试一下。

