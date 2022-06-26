# 高级进阶组合玩法——Aria2+Rclone实现OneDrive、Google Drive等网盘离线下载

## 目录

- [高级进阶组合玩法——Aria2+Rclone实现OneDrive、Google Drive等网盘离线下载](#高级进阶组合玩法aria2rclone实现onedrivegoogle-drive等网盘离线下载)
  - [目录](#目录)
  - [前言](#前言)
  - [准备工作](#准备工作)
  - [安装Aria2](#安装aria2)
  - [安装和配置Rclone](#安装和配置rclone)
  - [配置自动上传脚本](#配置自动上传脚本)
  - [检查配置是否成功](#检查配置是否成功)
  - [使用方法](#使用方法)

## 前言

Aria2有一个配置项`on-download-complete`，即在下载完后执行一个脚本或命令。当下载完成后Aria2会给脚本传递分别为GID、文件数量、文件路径的3个变量。利用这个配置项和这些变量就可以实现诸如下载完成后调用Rclone进行上传的操作。整个过程简单来说就是，Aria2下载文件到VPS，完成后告诉Rclone将文件上传到网盘。理论上只要是Rclone支持的网盘，都可以按照这个思路来实现**伪**离线下载。当然这篇文章并不是教你如何去写脚本，而是直接用已做好的一套方案，按照说明操作很容易就能实现。

## 准备工作

**首先你需要一个适合下载的VPS**

## 安装Aria2

这里使用Aria2一键安装脚本（项目自行搜索）

## 安装和配置Rclone

RCLONE官方提供了[一键安装脚本](https://rclone.org/install/#script-installati)：

```sh
curl https://rclone.org/install.sh | sudo bash
```

安装完后，输入`rclone config`命令进入交互式配置选项，按照提示一步一步来进行操作即可。如果你一脸懵逼，可以去看《[Rclone进阶——安装配置 连接OneDrive和Google Drive](https://github.com/mayjack0312/my-blog/blob/main/Rclone%E8%BF%9B%E9%98%B6%E2%80%94%E2%80%94%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE%20%E8%BF%9E%E6%8E%A5OneDrive%E5%92%8CGoogle%20Drive.md)》来了解配置的详细过程。

## 配置自动上传脚本

如果使用了已整合[Aria2完美配置](https://github.com/mayjack0312/aria2.conf)的Aria2一键脚本 ，安装后会附带一些附加功能脚本功能脚本，RCLONE自动上传脚本就是其中之一。由于默认不启用，所以需要手动启用。

> **TIPS：** 本项目的上传脚本使用更稳定快速的原生命令上传方式，而非处在测试阶段的挂载方式，这点和一般的脚本不同。

- 输入`nano /root/.aria2c/aria2.conf`打开Aria2配置文件进行修改。或使用Aria2一键安装脚本 增强版中的手动修改选项打开配置文件进行修改。找到“下载完成后执行的命令”，把`clean.sh`替换为`upload.sh`。

```sh
# 下载完成后执行的命令
on-download-complete=/root/.aria2c/upload.sh
```

> Linux我个人推荐使用：Vi&Vim（自带的nano在使用上有一定的难度）

- 输入`vim /root/.aria2c/script.conf`打开附加功能脚本配置文件进行修改，有中文注释，按照自己的实际情况进行修改，第一次使用只建议修改网盘名称。

```sh
# 网盘名称(RCLONE 配置时填写的 name)
drive-name=OneDrive
```

- 重启Aria2。脚本选项重启或者执行以下命令：

```sh
service aria2 restart
```

## 检查配置是否成功

- 执行`upload.sh`脚本，提示`success`即代上传脚本能正常被调用，否则请检查与RCLONE有关的配置。

```sh
/root/.aria2c/upload.sh
```

- 打开实时日志并下载任意文件，出现上传成功信息即代表配置成功，否则请认真阅读教程并重新开始。
- 检查网盘是否存在相关文件，若不存在说明你搞错网盘了。

## 使用方法

当进行完以上所有操作，现在下载文件就会自动上传至相应的网盘。

Aria2是命令行后端软件，需要配合前端GUI才能有更好的使用体验，对于从来没有接触过的萌新，建议去看《[Aria2前端面板（GUI、WebUI）AriaNg使用教程](https://github.com/mayjack0312/my-blog/blob/main/Aria2%E5%89%8D%E7%AB%AF%E9%9D%A2%E6%9D%BF%EF%BC%88GUI%E3%80%81WebUI%EF%BC%89AriaNg%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)》来了解相关基础知识。

