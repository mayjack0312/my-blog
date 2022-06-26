# Aria2前端面板（GUI、WebUI）AriaNg使用教程

## 目录

- [Aria2前端面板（GUI、WebUI）AriaNg使用教程](#aria2前端面板guiwebuiariang使用教程)
  - [目录](#目录)
  - [前言](#前言)
  - [安装&部署AriaNg](#安装部署ariang)
    - [网页](#网页)
    - [桌面应用程序](#桌面应用程序)
    - [Docker](#docker)
  - [AriaNg连接Aria2服务端](#ariang连接aria2服务端)
    - [手动设置](#手动设置)
    - [API一键设置](#api一键设置)
  - [AriaNg导入/导出设置](#ariang导入导出设置)
  - [AriaNg使用注意事项](#ariang使用注意事项)

## 前言

Aria2只是一个命令行程序（后端、服务端），配合前端面板（比如We­bUI）才会有很好的人机交互使用体验，在《在Windows中使用Aria2》这篇教程中博主曾推荐过一些Aria2的前端面板，Ar­i­aNg就是其中非常优秀的一个。早期使用Aria2的人群多数为极客群体，随着历史的进程逐渐从Linux普及到了Win­dows平台，为广大人民群众所熟知。手动配置是一个非常痛苦的过程，所以有些热心网友为了让更多的人在Win­dows上使用这个强大的下载工具于是就产生了各种Aria2+Ar­i­aNg整合版，有些干脆直接叫“Ar­i­aNg Win­dows版”、“Ar­i­aNg GUI”，还有很多VPS部署Aria2的教程往往会教如何去部署Ar­i­aNg页面，这就让很多人误以为是Ar­i­aNg在下载，模糊了萌新们对Aria2与Ar­i­aNg的认知。甚至能在Ar­i­aNg的is­sue中看到有关Aria2无法下载提问，估计开发者心中有一万个草泥马在奔跑。那么Ar­i­aNg到是什么？与Aria2有什么关系？如何使用呢？这篇文章将给你正确答案。

> **TIPS:** 如果你还没有安装过Aria2，推荐使用Aria2一键安装管理脚本增强版(GNU/​Linux)或[Aria2 Pro](https://github.com/mayjack0312/Aria2-Pro-Docker)(Docker)进行安装部署。

## 安装&部署AriaNg

### 网页

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625010926.png)

[AriaNg](https://github.com/mayswind/AriaNg)可以在项目的[releases](https://github.com/mayswind/AriaNg/releases)页面下载，其中标准版具有资源缓存和异步加载的特性，页面打开速度会更快，所以适合部署到Web服务器。而AllI­nOne版是一个单html文件，可以在本地直接打开使用。

> **科普：** Ar­i­aNg只是一个静态网页，只负责发送指令给Aria2服务端，所填写的RPC地址和RPC密钥等设置数据只会储存在本地浏览器中，不管是在本地直接打开使用还是访问在线网页使用都只是本地浏览器到远端Aria2服务的RPC协议通讯。所以部署到Web服务器不是必要操作，对页面设置密码更是徒劳。
**吐槽：** 有些“大神”的教程看似高大上，其实自己连基本原理都没弄明白，误导了很多萌新都把时间都浪费在了折腾Ar­i­aNg页面的部署中。

对于懒得部署和下载的小伙伴，可以使用以下这些已经部署好的Ar­i­aNg网页版：

> **TIPS：** HTTPS链接仅适用于RPC开启TLS的服务端，没有特殊需求使用HTTP链接即可。

- 由[AriaNg](https://github.com/mayswind/AriaNg)开发者提供的Demo页面：http://ariang.mayswind.net/latest

### 桌面应用程序

[AriaNg Native](https://github.com/mayswind/AriaNg-Native)是Ar­i­aNg开发者基于Elec­tron开发的Ar­i­aNg桌面应用程序，增加了一些[额外的功能](https://github.com/mayswind/AriaNg-Native#extra-featur)，比如拖拽种子文件添加任务。它支持Win­dows和ma­cOS，[下载](https://github.com/mayswind/AriaNg-Native/releases)安装后打开就能使用，不需要使用浏览器。

![iamge](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625012225.png)

### Docker

Docker是目前非常流行的一键部署方式之一。为了方便小伙伴们自行部署Ar­i­aNg页面，博主特意制作了可能是目前体积和资源占用最小且加载速度最快的Ar­i­aNg页面的Docker镜像，此外还解除了单服务器线程数和文件分片大小等设置的限制，可更加方便的配合[Aria2 Pro](https://github.com/mayjack0312/Aria2-Pro-Docker)使用。启动命令示例如下，启动后访问所在主机的`6880`端口即可。

```Docker
# bridge 网络模式
docker run -d \
    --name ariang \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -p 6880:6880 \
    p3terx/ariang

# host 网络模式（如果你需要使用 IPv6 网络访问，这是最简单的方式）
docker run -d \
    --name ariang \
    --log-opt max-size=1m \
    --restart unless-stopped \
    --network host \
    p3terx/ariang --port 6880 --ipv6
```

## AriaNg连接Aria2服务端

### 手动设置

在AriaNg设置中填写相关RPC信息。RPC地址对应IP或域名，RPC秘钥对应配置文件(aria2.conf)中rpc-secret选项后面的参数。如果没有过修改端口，那么默认是`6800`无需修改。

以Aria2一键安装管理脚本增强版为例子，安装后会显示这些信息，能非常直观的进行设置。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625012624.png)

> **TIPS：** 无法连接一般是两种情况导致的：1.`6800`端口未开放。 2. 网络不通畅。

### API一键设置

Ar­i­aNg提供了一个名为命令行API的功能，这使得我们可以通过URL请求的方式去一键设置RPC信息，而不必每次换浏览器和设备都重复手动设置。最新的Aria2一键安装管理脚本增强版中所生成的Ar­i­aNg一键直达链接就是使用的这个功能。

API RPC设置语法：

```
/#!/settings/rpc/set/${protocol}/${rpcHost}/${rpcPort}/${rpcInterface}/${secret}
```

- ${protocol} 为连接协议. 可以使用http、https、ws或wss。
- ${rpcHost} 为 Aria2 RPC 的主机。可以使用 IP 或域名。
- ${rpcPort} 为 Aria2 RPC 的端口。
- ${rpcInterface} 为 Aria2 RPC 的路径，默认为jsonrpc。
- ${secret} 为使用 URL 安全的base64编码后的 RPC 密钥，可选。

举个例子，假设使用`http://aria2c.com`这个Ar­i­aNg页面，连接协议使用ws，主机IP为：233.233.233.233，端口为：6800，RPC路径使用默认的jsonrpc，RPC密钥为：P3TERX。

在终端中通过base64编码RPC密钥（或者也可以使用在线编码工具）：

```sh
$ echo -n "P3TERX" | base64 # 输入命令
UDNURVJY # 编码结果
```

综合以上信息可以如下的链接：

```
http://aria2c.com/#!/settings/rpc/set/ws/233.233.233.233/6800/jsonrpc/UDNURVJY
```

将配置好的链接保存到浏览器书签，可以方便的同步到不同的设备直接进行访问。

## AriaNg导入/导出设置

如果你像博主一样使用Aria2下载的VPS或者设备非常多而且密码都是随机生成的，这时到另一个设备去使用一个个手动去添加那是非常麻烦的事情，除了前面提到的**API一键设置**的方法，还可以使用**导入/导出设置**功能。它可以把所有的RPC信息以json格式保存备份，然后导入到其它设备使用。

> **TIPS：** 使用Ar­i­aNg网页版通过API链接方式会覆盖掉之前保存的所有RPC信息。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625013524.png)

## AriaNg使用注意事项

- 在AriaNg（或其它前端面板）中对Aria2设置的修改只会保存在内存中，不会修改配置文件，即属于临时修改，只在当前运行的进程中有效。Aria2重启或关闭后会重新读取配置文件。这是Aria2的特性决定的并是不bug，所以必要的设置需写入到Aria2配置文件中。
- 修改Aria2配置文件后，要先关闭AriaNg再重启Aria2进程，否则AriaNg可能会传递之前缓存的配置给Aria2，这可能会导致修改的配置没有生效。理论上其它前端面板也是这样。（待讨论）
- HTTP协议的**RPC请求方式**只建议使用POST，否则可能导致BT种子无法传递到Aria2服务端。
- BT下载多文件时，若想取消部分文件，需先暂停任务后才能进行勾选。

