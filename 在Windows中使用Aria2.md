# 在Windows中使用Aria2

## 目录

- [在Windows中使用Aria2](#在windows中使用aria2)
  - [目录](#目录)
  - [下载&安装](#下载安装)
  - [配置](#配置)
  - [隐藏cmd窗口运行](#隐藏cmd窗口运行)
  - [开机启动](#开机启动)
  - [Aria2管理面板](#aria2管理面板)
    - [Web UI](#web-ui)
    - [本地程序](#本地程序)
    - [Chrome插件](#chrome插件)

## 下载&安装

Aria2下载地址：https://github.com/aria2/aria2/releases
解压后有一堆文件，其中aria2c.exe就是主程序，其余的是说明文件，可以删除。
把aria2c.exe放到任意文件夹，比如D:/aria2。
在同目录下新建几个文件：
aria2.session（下载历史，空文件就行）
aria2.conf（配置文件）
HideRun.vbs（隐藏cmd窗口运行Aria2的脚本）

## 配置

用文本编辑工具打开刚才建立的aria2.conf。
以下为自用的配置，直接复制保存即可，也可根据自己的需求修改。完整配置说明可参考官方文档。

```conf
## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##

# 日志
log=aria2.log
log-level=error

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
dir=E:\Downloads
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
file-allocation=falloc
# 断点续传
continue=true

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
max-concurrent-downloads=3
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=5
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
split=5
# 整体下载速度限制, 运行时可修改, 默认:0
#max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0
max-overall-upload-limit=1M
# 单个任务上传速度限制, 默认:0
#max-upload-limit=0
# 禁用IPv6, 默认:false
disable-ipv6=true

## 进度保存相关 ##

# 从会话文件中读取下载任务
input-file=aria2.session
# 在Aria2退出时保存`错误/未完成`的下载任务到会话文件
save-session=aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
save-session-interval=60

## RPC相关设置 ##

# 启用RPC, 默认:false
enable-rpc=true
# 允许所有来源, 默认:false
rpc-allow-origin-all=true
# 允许非外部访问, 默认:false
rpc-listen-all=true
# 事件轮询方式, 取值:[epoll, kqueue, port, poll, select], 不同系统默认值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修改, 默认:6800
rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 取代 --rpc-user 和 --rpc-passwd 选项
#rpc-secret=<TOKEN>
# 设置的RPC访问用户名, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-user=<USER>
# 设置的RPC访问密码, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-passwd=<PASSWD>

## BT/PT下载相关 ##

# 当下载的是一个种子(以.torrent结尾)时, 自动开始BT任务, 默认:true
follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 默认:6881-6999
listen-port=51413-52333
# 单个种子最大连接数，0表示不限制，默认:55
bt-max-peers=0
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=true
# 打开IPv6 DHT功能, PT需要禁用
enable-dht6=true
# DHT网络监听端口, 默认:6881-6999
dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 默认:false
#bt-enable-lpd=false
# 种子交换, PT需要禁用, 默认:true
enable-peer-exchange=true
# 期望下载速度, 对少种的PT很有用, 默认:50K
bt-request-peer-speed-limit=50M
# 客户端伪装, PT需要
peer-id-prefix=-UT3500-
user-agent=uTorrent/3500(43580)
# 最小分享率。当种子的分享率达到这个数时, 自动停止做种, 0为一直做种, 默认:1.0
seed-ratio=0
# 最小做种时间。此选项设置为0时，将在BT任务下载完成后不进行做种。
seed-time=0
# 强制保存会话, 即使任务已经完成, 默认:false
# 较新的版本开启后会在任务完成后依然保留.aria2文件
force-save=true
# BT校验相关, 默认:true
#bt-hash-check-seed=true
# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true
# 保存磁力链接元数据为种子文件(.torrent文件), 默认:false
#bt-save-metadata=false
# 删除未选择文件
bt-remove-unselected-file=true

## BT 服务器地址 ##
bt-tracker=udp://tracker.coppersurfer.tk:6969/announce,udp://tracker.opentrackr.org:1337/announce,udp://9.rarbg.to:2710/announce,http://tracker.internetwarriors.net:1337/announce,udp://exodus.desync.com:6969/announce,udp://explodie.org:6969/announce,http://explodie.org:6969/announce,http://tracker1.itzmx.com:8080/announce,udp://tracker1.itzmx.com:8080/announce,udp://ipv4.tracker.harry.lu:80/announce,udp://denis.stalker.upeer.me:6969/announce,udp://tracker.port443.xyz:6969/announce,udp://open.demonii.si:1337/announce,http://tracker.port443.xyz:6969/announce,udp://tracker.torrent.eu.org:451/announce,udp://tracker.tiny-vps.com:6969/announce,udp://tracker.iamhansen.xyz:2000/announce,udp://thetracker.org:80/announce,udp://retracker.lanta-net.ru:2710/announce,udp://open.stealth.si:80/announce,udp://bt.xxx-tracker.com:2710/announce,http://private.minimafia.nl:443/announce,http://prestige.minimafia.nl:443/announce,http://open.acgnxtracker.com:80/announce,udp://tracker.vanitycore.co:6969/announce,udp://zephir.monocul.us:6969/announce,udp://tracker.cyberia.is:6969/announce,https://tracker.fastdownload.xyz:443/announce,https://opentracker.xyz:443/announce,http://tracker3.itzmx.com:6961/announce,http://opentracker.xyz:80/announce,http://open.trackerlist.xyz:80/announce,http://torrent.nwps.ws:80/announce,udp://tracker2.itzmx.com:6961/announce,udp://tracker1.wasabii.com.tw:6969/announce,udp://tracker.gbitt.info:80/announce,udp://tracker.filepit.to:6969/announce,udp://tracker.dyn.im:6969/announce,udp://tracker.dler.org:6969/announce,udp://torrentclub.tech:6969/announce,udp://pubt.in:2710/announce,udp://bittracker.ru:6969/announce,https://tracker.gbitt.info:443/announce,http://tracker2.itzmx.com:6961/announce,http://tracker1.wasabii.com.tw:6969/announce,http://tracker.torrentyorg.pl:80/announce,http://tracker.gbitt.info:80/announce,http://tracker.city9x.com:2710/announce,http://tracker.btsync.gq:233/announce,http://torrentclub.tech:6969/announce,http://t.nyaatracker.com:80/announce,http://retracker.mgts.by:80/announce,http://open.acgtracker.com:1096/announce,http://fxtt.ru:80/announce,http://0d.kebhana.mx:443/announce,wss://tracker.openwebtorrent.com:443/announce,wss://tracker.fastcast.nz:443/announce,wss://tracker.btorrent.xyz:443/announce,udp://tracker4.itzmx.com:2710/announce,udp://tracker.justseed.it:1337/announce,udp://packages.crunchbangplusplus.org:6969/announce,https://1337.abcvg.info:443/announce,http://tracker4.itzmx.com:2710/announce,http://tracker.tfile.me:80/announce.php,http://tracker.tfile.me:80/announce,http://tracker.tfile.co:80/announce,http://share.camoe.cn:8080/announce,http://peersteers.org:80/announce,http://omg.wtftrackr.pw:1337/announce
```

已经内置了一些[BT服务器地址](https://github.com/ngosang/trackerslist)，以保证BT的下载速度和成功率。

## 隐藏cmd窗口运行

用文本编辑工具打开刚才建立的HideRun.vbs，复制如下代码保存。

```Vb
CreateObject("WScript.Shell").Run "aria2c.exe --conf-path=aria2.conf",0
```

打开HideRun.vbs这个文件，即可实现隐藏cmd窗口运行Aria2。

## 开机启动

设置开机启动不必担心浪费系统资源，因为Aria2非常轻量，只占用2M多的内存。
打开“运行”对话框（WIN+R），输入以下任一命令，回车或点击“确定”即可打开“系统启动文件夹”。

```Shell
shell:Common Startup
```

```Shell
%programdata%\Microsoft\Windows\Start Menu\Programs\Startup
```

创建HideRun.vbs文件的快捷方式，放到启动文件夹内即可添加开机启动。

## Aria2管理面板

Aria2是命令行下载器，是没有界面的，需要配合前端面板才能更加便利的使用。

### Web UI

最简单的方式就是访问这几个网址：

AriaNg：http://ariang.mayswind.net/latest（强烈推荐）  
[WebUI-Aria2](https://github.com/ziahamza/webui-aria2)：/  
Photon-WebUI：/  
[YAAW中文版](https://github.com/aa65535/yaaw-zh-hans)：http://aria2c.com/  

使用教程可参考《[Aria2前端面板AriaNg使用教程](https://github.com/mayjack0312/my-blog/blob/main/Aria2%E5%89%8D%E7%AB%AF%E9%9D%A2%E6%9D%BF%EF%BC%88GUI%E3%80%81WebUI%EF%BC%89AriaNg%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)》

### 本地程序

[AriaNg Native](https://github.com/mayswind/AriaNg-Native/releases)

### Chrome插件

迅雷离线Aria2

