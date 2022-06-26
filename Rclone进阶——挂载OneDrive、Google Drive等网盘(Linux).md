# Rclone进阶——挂载OneDrive、Google Drive等网盘(Linux)

## 目录

- [Rclone进阶——挂载OneDrive、Google Drive等网盘(Linux)](#rclone进阶挂载onedrivegoogle-drive等网盘linux)
  - [目录](#目录)
  - [前言](#前言)
  - [安装和配置Rclone](#安装和配置rclone)
  - [安装fuse](#安装fuse)
  - [挂载网盘](#挂载网盘)
    - [手动挂载](#手动挂载)
      - [使用示例](#使用示例)
    - [开机自动挂载](#开机自动挂载)
      - [管理](#管理)
      - [卸载自启挂载](#卸载自启挂载)

## 前言

Rclone是一个的命令行工具，支持在不同对象存储、网盘间同步、上传、下载数据。并且通过一些设置可以实现离线下载、服务器备份等非常实用的功能。Rclone有很多种使用方式，挂载是其中的一种。

> **友情提示：** 挂载这个操作并不是必须的，作为一个实验性功能它有很多局限性和问题。挂载后并不能当做一个真正的磁盘来使用，在进行文件操作时会使用本地磁盘进行缓存，即占用本地磁盘空间。使用不当还可能造成磁盘写满、VPS卡死等问题。在Google上搜索“Rclone”，与之相关的最多的关键词就是“挂载”，这在一定程度上对很多刚接触的小伙伴造成了误导。要稳定的进行上传、下载、同步等操作建议使用 Rclone的原生命令功能，使用方法参见《[Rclone进阶——常用命令参数详解](https://github.com/mayjack0312/my-blog/blob/main/Rclone%E8%BF%9B%E9%98%B6%E2%80%94%E2%80%94%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E5%8F%82%E6%95%B0%E8%AF%A6%E8%A7%A3.md)》。

## 安装和配置Rclone

官方提供了[一键安装脚本](https://rclone.org/install/#script-installati)：

```sh
curl https://rclone.org/install.sh | sudo bash
```

安装完后，输入`rclone config`命令进入交互式配置选项，按照提示一步一步来进行操作即可。如果你一脸懵逼，可以去看《[Rclone进阶——安装配置 连接OneDrive和Google Drive](https://github.com/mayjack0312/my-blog/blob/main/Rclone%E8%BF%9B%E9%98%B6%E2%80%94%E2%80%94%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE%20%E8%BF%9E%E6%8E%A5OneDrive%E5%92%8CGoogle%20Drive.md)》来了解配置的详细过程。

## 安装fuse

挂载需要安装fuse，根据自己的系统来选择安装命令：

```sh
# Debian/Ubantu
apt-get update && apt-get install -y fuse
# CentOS
yum install -y fuse
```

## 挂载网盘

挂载网盘分为手动挂载和开机自动挂载，根据自己的需求来选择。

### 手动挂载

```txt
# 挂载
rclone mount <网盘名称:网盘路径> <本地路径> [参数] --daemon

# 取消挂载
fusermount -qzu <本地路径>
```

`网盘名称`为配置时填的`name`，网盘路径为网盘里的文件夹，留空为整个网盘，`本地路径`为VPS上的本地文件夹。`参数`可以查看[官方文档](https://rclone.org/commands/rclone_mount/)根据需求进行选择。实际输入时不要有括号，这里只是为了更清楚的区分。`--daemon`为进程守护参数，可后台运行。

#### 使用示例

输入命令进行挂载操作：

```txt
rclone mount Onedrive:/ /Onedrive --copy-links --allow-other --allow-non-empty --umask 000 --daemon
```

然后输入`df -h`命令查看挂载情况。

```txt
root@LMY:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            286M     0  286M   0% /dev
tmpfs            60M  7.8M   52M  14% /run
/dev/sda1        99G   25G   71G  26% /
tmpfs           297M   24K  297M   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           297M     0  297M   0% /sys/fs/cgroup
Onedrive:       5.0T  216G  4.8T   5% /Onedrive # 这个就是挂载的网盘
```

取消挂载：

```txt
fusermount -qzu /Onedrive
```

### 开机自动挂载

- 下载并编辑自启脚本：

```sh
wget -N git.io/rcloned && nano rcloned
```

- 修改内容：

```sh
NAME="Onedrive" # Rclone配置时填写的name
REMOTE=''  # 远程文件夹，网盘里的挂载的一个文件夹，留空为整个网盘
LOCAL='/Onedrive'  # 挂载地址，VPS本地挂载目录
```

- 设置开机自启：

```sh
mv rcloned /etc/init.d/rcloned
chmod +x /etc/init.d/rcloned
update-rc.d -f rcloned defaults # Debian/Ubuntu
chkconfig rcloned on # CentOS
bash /etc/init.d/rcloned start
```

看到 `[信息]rclone启动成功!`即可。

#### 管理

开始挂载`bash /etc/init.d/rcloned start`
停止挂载`bash /etc/init.d/rcloned stop`
重新挂载`bash /etc/init.d/rcloned restart`
查看日志`tail -f /$HOME/.rclone/rcloned.log`

#### 卸载自启挂载

```sh
bash /etc/init.d/rcloned stop
update-rc.d -f rcloned remove # Debian/Ubuntu
chkconfig rcloned off # CentOS
rm -f /etc/init.d/rcloned
```
