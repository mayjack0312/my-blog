# Aria2 新手入门

## 目录

- [Aria2 新手入门](#aria2-新手入门)
  - [目录](#目录)
  - [前言](#前言)
  - [使用场景](#使用场景)
    - [在电脑端下载](#在电脑端下载)
    - [使用路由器离线下载](#使用路由器离线下载)
    - [使用NAS离线下载](#使用nas离线下载)
    - [使用VPS离线下载](#使用vps离线下载)
    - [给OneDrive和Google Drive添加离线下载功能](#给onedrive和google-drive添加离线下载功能)
    - [百度网盘转存到其它网盘](#百度网盘转存到其它网盘)
  - [相关资料集](#相关资料集)
    - [官方资料](#官方资料)
    - [前端面板(Web UI)](#前端面板web-ui)
    - [本地程序](#本地程序)
    - [浏览器插件](#浏览器插件)

## 前言

Aria2是一个功能非常强大且功能非常齐全的下载工具，它支持BT、磁力、HTTP、FTP等下载协议，常用做离线下载的服务端。由于过去写了一些关于Aria2的教程，没想到传播度非常不错。这些教程主要是讲一些使用的技巧，对于使用过Aria2的人看说，是非常容易理解的。由于不是保姆级教程，而且过于碎片化，萌新看起来往往是一脸懵逼的。这篇文章将会结合我以前的教程和经历，从不同的使用场景角度讲解如何使用Aria2下载。至于想要精通，只有去研究看了也不知道怎么用的[Aria2官方文档](https://aria2.github.io/manual/en/html/index.html)。

## 使用场景

### 在电脑端下载

如果你只是想替代迅雷这样的本地下载软件，简单的在电脑桌面操作系统上使用下载这个功能，那么你可以尝试使用基于Aria2打造的全能下载工具Motrix，操作系统方面它做到了Win­dows、ma­cOS、Linux全平台支持。使用它几乎不需要学习成本，就跟你原来使用迅雷一样，直接下载即可。而且它还支持解析迅雷的链接，这是其他下载工具所不具备的，不过唯一遗憾是由于Aria2不支持ED2K协议，Motrix自然也是不支持的。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/24/20220624234604.png)

在[Motrix](https://motrix.app/)出现前。如果需要在Win­dows电脑上使用Aria2，首先就需要了解如何配置Aria2，这是相当复杂的过程，在我刚接触Aria2时，经历过很长一段时间折腾，才终于写下了《[在Windows中使用Aria2](https://github.com/mayjack0312/my-blog/blob/main/%E5%9C%A8Windows%E4%B8%AD%E4%BD%BF%E7%94%A8Aria2.md)》这篇教程。

Aria2的强大之处在于丰富的自定义配置和超低的资源消耗，启动后内存仅占用4M左右，而Motrix的UI由于是基于Elec­tron开发，所以启动后达到了惊人的200M，所以在低配置的机器上使用体验是相当糟糕的，而且设置选项上并没有给太多的自定义空间，难免有些遗憾。

可能由于Motrix开发者对Aria2理解不足，在Aria2的配置方面很随意，这也导致了Motrix空有一个漂亮的外壳，而实际下载体验非常糟糕，尤其是BT下载。博主在体验过一段时间后还是用回了自己配置的Aria2+Ar­i­aNgNa­tive组合。如果你不想自己去配置Aria2，可以使用其它的工具去替代，比如BT下载可以使用[qBittorrent](https://www.qbittorrent.org/)，多线程下载可以使用IDM、[FDM](https://www.freedownloadmanager.org)。

### 使用路由器离线下载

开着电脑下载又很费电，此时当你的路由器固件带有Aria2功能，你就可以使用它进行离线下载。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/24/20220624235114.png)

使用方法很简单，开启路由器上的Aria2，使用前端面板比如[AriaNg](https://github.com/mayswind/AriaNg)连接路由器上的Aria2后端程序。除非你自己进行过设置，一般路由器上的Aria2默认没有RPC秘钥，只需要RPC地址处直接填写路由器的IP即可。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/24/20220624235340.png)

### 使用NAS离线下载

NAS操作系统比如像群辉、Open­Medi­aVault、Un­raid等都带有Docker功能，使得我们可以方便的部署Aria2等下载工具。

[Aria2 Pro](https://github.com/mayjack0312/Aria2-Pro-Docker)是博主基于[Aria2完美配置](https://github.com/mayjack0312/aria2.conf)方案精心制作的Docker镜像，使用它你不会遇到其它Aria2 Docker方案会遇到的无法下载、失任务进度、重复下载等现象，而且还有相当高的可玩性和自定义性。感兴趣的小伙伴可以去查看[说明文档]()。

### 使用VPS离线下载

由于中国大陆的网络环境和迅雷对BT下载的迫害，进行BT下载的速度非常慢，甚至没有速度。有很长一段时间博主很少使用Aria2在本地路由和NAS上使用BT下载，一般只用于取回VPS或网盘上的文件。

百度网盘的离线下载很好用人尽皆知，但并不是所有资源都能下载。这时如果你有一个装有Aria2的境外VPS，很多在中国大陆无法下载的资源都能直接下载，而且取回的速度比百度网盘快（除非使用了第三方百度网盘工具破解）。
**后续可能会考虑出VPS推荐帖（目前没这打算）**

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/24/20220624235952.png)

如何操作呢？只需要在VPS上使用Aria2一键安装管理脚本增强版或者[Aria2 Pro](https://github.com/mayjack0312/Aria2-Pro-Docker)(Docker)进行部署，使用前端面板比如Ar­i­aNg连接到VPS上的Aria2后端程序，最后只需要下载这个操作即可。前后端连接的具体方法可以看《[Aria2前端面板(GUI)AriaNg 使用教程](https://github.com/mayjack0312/my-blog/blob/main/Aria2%E5%89%8D%E7%AB%AF%E9%9D%A2%E6%9D%BF%EF%BC%88GUI%E3%80%81WebUI%EF%BC%89AriaNg%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)》。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625000420.png)

下载完成后可以使用Xftp、FileZilla这类SFTP工具进行取回，或者在VPS上搭建Nextcloud、File Browser等网盘服务进行下载。如果你的VPS上装有宝塔面板，可以从管理后台进行下载。

### 给OneDrive和Google Drive添加离线下载功能

VPS毕竟容量和流量有限，而一些大容量不限速的网盘（比如 OneDrive）又没有离线下载功能，一个比较笨的方法是，先下载到VPS然后通过RCLONE手动上传。但手动的操作可以通过编程实现自动化，于是有许许多多大佬给出了自己的自动化方案。然而现实很残酷，它们并不好用。于是博主开始着手编写自己的方案，并添加到了[Aria2完美配置](https://github.com/mayjack0312/aria2.conf)项目中。经过半年多时间的小修小改，已经相当完美了。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625000834.png)

根据《[Aria2+Rclone实现OneDrive、Google Drive等网盘离线下载]()》这篇文章进行简单的配置，再配合[AriaNg](https://github.com/mayswind/AriaNg)下载，可能喝口水、上个厕所的时间，你需要的资源就在网盘上了。至于取回可以去相应的网盘下载，还可以使用像[OLAINDEX](https://github.com/WangNingkai/OLAINDEX)这样的OneDrive目录索引程序搭建下载站，不仅方便自己取回，也方便分享。这样与百度网盘的离线下载体验几乎没有什么区别了，甚至可能更好，毕竟没有资源上的限制。

### 百度网盘转存到其它网盘

由于中国大陆未来对互联网的政策不明朗，以及近几年各种网盘倒闭、百度网限速等因素，把百度网盘里的资源转出是个很好的选择。

百度网盘转存是上个技巧的延伸，只不过是下载源变成了百度网盘，需要用到第三方百度网盘客户端。实现方法可以看《[百度网盘转存到OneDrive、Google Drive等其他网盘]()》这篇文章。

## 相关资料集

### 官方资料

- [Aria2 源码](https://github.com/aria2/aria2)
- [Aria2 文档](https://aria2.github.io/manual/en/html/index.html)

### 前端面板(Web UI)

REPO|DEMO
:---:|:---:
[AriaNg](https://github.com/mayswind/AriaNg)|http://ariang.mayswind.net/latest
[WebUI-Aria2](https://github.com/ziahamza/webui-aria2)|/
Photon-WebUI|/
[YAAW中文版](https://github.com/aa65535/yaaw-zh-hans)|/

### 本地程序

名称|介绍
:---|:---
[AriaNg Native](https://github.com/mayswind/AriaNg-Native)|非常优秀的本地前端程序，需要配合Aria2后端程序使用。
[PanDownload](https://pandownload.com/)|可能是最好用的第三方百度网盘下载工具，基于Aria2，也可作为远程Aria2的前端，仅支持下载百度网盘的资源。不支持RPC https连接。
SpeedPan|另一款基于Aria2的第三方百度网盘下载工具，支持RPC https连接。
[Motrix](https://motrix.app/)|基于Aria2的全能的下载工具，支持下载HTTP、FTP、BT、磁力链、百度网盘等资源，但不支持连接其它Aria2后端，仅能作为本地下载工具。
Photon|基于Aria2的下载工具，功能简单，仅能作为本地下载工具。

### 浏览器插件

- Aria2 manager
- Aria2助手

