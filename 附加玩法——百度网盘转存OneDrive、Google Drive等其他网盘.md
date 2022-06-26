# 附加玩法——百度网盘转存OneDrive、Google Drive等其他网盘

## 目录

- [附加玩法——百度网盘转存OneDrive、Google Drive等其他网盘](#附加玩法百度网盘转存onedrivegoogle-drive等其他网盘)
  - [目录](#目录)
  - [前言](#前言)
  - [准备工作](#准备工作)
  - [安装及配置自动上传脚本](#安装及配置自动上传脚本)
  - [设置第三方百度网盘工具](#设置第三方百度网盘工具)

## 前言

第三方百度网盘工具（以下简称度盘工具）多数是基于Aria2来进行下载的，所以同时包含了Aria2前端和后端程序，而某些度盘工具会有“远程Aria2”功能（不同度盘工具的功能命名可能会有差异），这个功能就相当于你把度盘工具当做了VPS上Aria2的前端程序。和使用其它前端类似，你只需要设置VPS上Aria2的RPC信息（地址、端口、密钥）及下载路径。设置完成后从度盘工具下载文件会直接下载到VPS，然后再通过自动上传脚本在下载完成后调用Rclone上传到相应的网盘，这就实现了百度网盘的转存操作。整个过程简单来讲就是：度盘工具发送下载请求→Aria2下载到VPS→Rclone上传到OneDrive或Google Drive。如果你对Aria2的使用驾轻就熟后，这个操作对于你来说非常简单。

## 准备工作

**VPS是必须的**

## 安装及配置自动上传脚本

参考《[高级进阶组合玩法——Aria2+Rclone实现OneDrive、Google Drive等网盘离线下载](https://github.com/mayjack0312/my-blog/blob/main/%E9%AB%98%E7%BA%A7%E8%BF%9B%E9%98%B6%E7%BB%84%E5%90%88%E7%8E%A9%E6%B3%95%E2%80%94%E2%80%94Aria2%2BRclone%E5%AE%9E%E7%8E%B0OneDrive%E3%80%81Google%20Drive%E7%AD%89%E7%BD%91%E7%9B%98%E7%A6%BB%E7%BA%BF%E4%B8%8B%E8%BD%BD.md)》这篇教程来了解Aria2的安装和配置、Rclone的安装和配置及自动上传脚本的配置详细过程，这里不再赘述。

## 设置第三方百度网盘工具

如果你对RPC设置还不了解，推荐你去看《[Aria2前端面板（GUI、WebUI）AriaNg使用教程](https://github.com/mayjack0312/my-blog/blob/main/Aria2%E5%89%8D%E7%AB%AF%E9%9D%A2%E6%9D%BF%EF%BC%88GUI%E3%80%81WebUI%EF%BC%89AriaNg%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)》来了解相关信息，这对于你理解下面的操作会很有帮助。

这里以速盘（Speed­Pan）为例，来进行设置，其它具有远程Aria2功能的度盘工具的操作都是类似的，无非就是 填写IP地址、端口、密码，非常简单。直接看图：

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626120801.png)

> **注意事项：** 下载路径必须设定成和VPS上的Aria2配置文件中的下载路径一致，才能正常触发自动上传脚本。
**吐槽：** 这里的例子本来是PanDown­load，但在我写这篇教程时发现PanDown­load移除了远程Aria2功能。不过好在后来又加回来了。

**★★★ 时间截止到现在，PD好像挂了OwO**

当你进行完以上所有操作，通过度盘工具从百度网盘下载文件就相当于转存到了相应的网盘。
