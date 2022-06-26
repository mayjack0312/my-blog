# Aria2+Rclone+Onedrive实现全自动化下载

## 目录

- [Aria2+Rclone+Onedrive实现全自动化下载](#aria2rcloneonedrive实现全自动化下载)
  - [目录](#目录)
  - [FAQ](#faq)
  - [前提条件](#前提条件)
  - [配置Onedrive API](#配置onedrive-api)
    - [获取Client ID](#获取client-id)
    - [获取Client secret](#获取client-secret)
  - [设置API权限](#设置api权限)
  - [安装Rclone](#安装rclone)
    - [配置Rclone](#配置rclone)
      - [自动化配置](#自动化配置)
      - [手动配置](#手动配置)
  - [安装Aria2以及AriaNG](#安装aria2以及ariang)
  - [配置全自动上传脚本](#配置全自动上传脚本)
  - [测试可用性](#测试可用性)

## FAQ

- 为什么要Onedrive？

1. 便宜。
2. 可白嫖。
3. 客户端支援良好。
4. 文件分享方便。
5. **没有被墙 ！！！**

- 为什么用Aria2？

1. 支援广泛的下载协议，包括http(s)，ftp(s)，Bittorrent，ssh等。
2. 客户端支援良好。
3. 下载速度快，支援多线程下载。
4. 支持开源第三方GUI。

- 为什么用Rclone？

1. 方便好用。
2. Aria2不支援直接写入Onedrive，需要通过API中间件写入。
3. Rclone同时支援其他的云端服务，功能强大。

- 如何获得Onedrive？

1. 自购，可以上拼车（Office365家庭版 容量1T）一年大概90左右（唯一的缺点就是需要**找稳的**）偶尔会翻个车
2. 一类白嫖，非常容易翻车 几率大概是80%（非常不建议折腾）
3. 二类白嫖，edu邮箱的A1&A1P（客户端无法使用，貌似也无法调用Rclone）**直接Pass**
4. 三类白嫖，E3 MSDN（说实话，这个我到现在也没搞到）非常推荐搞一个，就是难度很大（号少，对应的优点就出来了：这类账号一般非常稳）
5. 四类白嫖，E5开发者（我现在就用的这个）这类号非常适合开发使用（你是全局管理员，API权限在你自己手里、而且是全齐的）这类对于非开发来说不太好折腾（它的账号续期需要不间断长期调用API才行）这类账号的优势在于禁得住折腾，可玩性强。

- 全自动化下载流程？

1. 短答案：Aria客户端发送请求后一段时间后，文件就会出现在Onedrive里面了。
2. 长答案：Aria客户端发送下载命令到服务器(VPS之类的)–>服务器接到请求开始下载，下载完成–>调用全自动化上传脚本–>调用Rclone命令进行上传，上传完成–>Onedrive客户端进行同步–>文件就在Onedrive里了。

## 前提条件

1. 一台VPS
2. 一个可用域名
3. 可用Onedrive账号

---

## 配置Onedrive API

> 如果你不希望看见上传失败或者上传速度特别慢这种事情，请完成此步骤。

SRC URL: https://rclone.org/onedrive/#getting-your-own-client-id-and-key

### 获取Client ID

- 进入[Microsoft Azure应用注册](https://portal.azure.com/#home)页面。点击`新注册`。**(首选Chrome浏览器)**

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625115435.png)

- 名称随意，账户类型选择最后一个，重定向URL填写`http://localhost:53682/`。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626135919.png)

- 创建成功后你会看到Client ID（客户端ID），复制并保存好。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625120320.png)

### 获取Client secret

- 点击`证书和密码`，按照图中序号的顺序操作添加密码。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625120840.png)

- 然后你会看到Value（值）和Client secret（客户端密码），复制并保存好。

    ![iamge](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626140235.png)

> [更新](https://www.jianshu.com/p/072ff75d35ca)

## 设置API权限

由于**rclone官网提供的教程有问题，会导致rclone token过期，因此请使用以下权限设置**。

- 点击`API权限`，按照图示进行操作，添加`Files.Read.All`、`Files.ReadWrite.All`、`User.Read`、`User.Read.All`这些权限。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625121508.png)

- 最后确认下权限是否添加完整。**(按照上面要添加的内容来，我这个截图不是这个项目)**

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625121637.png)

- 红框中的两项需要全选中

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626141240.png)

- 以下选项改为`是`**（最后别忘点Save）**

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626141537.png)

这样Onedrive API就配置完成了。

以下两个值需要复制出来，后面需要用到，分别是`应用程序（客户端）识别码`以及`客户端密码`的值。

---

## 安装Rclone

[安装](https://github.com/rclone/rclone)

```sh
curl https://rclone.org/install.sh | bash
apt-get install fuse -y
```

### 配置Rclone

#### 自动化配置

> 更新：新增全自动化配置方法

运行以下命令以实现全自动化配置

```sh
curl -Ss https://raw.githubusercontent.com/johnrosen1/vpstoolbox/master/install/rclone_config.sh | sudo bash
```

流程和手动差不多，但不用担心输错问题。

#### 手动配置

```sh
rclone config
```

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626142223.png)

选择`New remote`，输入`n`

![iamge](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626142309.png)

填入`name`，这个变量后面要用到，方便起见，建议直接填`onedrive`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626142402.png)

找到`26 / Microsoft OneDrive`，填入前面的数字26 **（序号可能随着更新而发生改变，请勿照抄）**

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626142558.png)

这里的`client_id`填入之前获取的`应用程序（客户端）识别码`

> 脚本默认值为xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx（理论有效期为永久）。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626142817.png)

这里的`client_secret`填入之前获取的`客户端密码`

> 脚本默认值为xxx_xxxxxxx-xxxxxx.xxxxxxxx-xxxx_x（理论有效期为永久）。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626143025.png)

选择你的Onedrive类型，我个人用的开发者版，选`1`。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626143401.png)

不需要配置高级设置，选`n`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626143451.png)

由于是在VPS上进行操作的，VPS没有浏览器，故选`n`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626143614.png)

由于Onedrive需要Outh认证，需要浏览器操作，故需要本地也安装Rclone，[前往](https://rclone.org/downloads/)下载安装。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626143740.png)

因为这是个CLI软件，使用我们需要使用`powershell`进行操作。快捷键`Win+X`，打开`Windows Powershell（管理员）`。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626144045.png)

找到解压后的文件路径并复制

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626144354.png)

在Powershell中输入`cd 你的路径`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626144554.png)

输入VPS中显示的命令，注意命令前面需要添加一个`.\`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626144650.png)

> 更新：这里推荐使用Firefox的隐私窗口并禁用Cookie以继续，否则后期可能导致Token expire问题。

会弹出一个浏览器窗口验证，全部选择是即可，成功后会出现以下内容：

> 用国内edu.cn邮箱白嫖的A1订阅有几率在此处失败，因此不推荐使用。**（有渠道的也可以考虑国外）**

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626144940.png)

复制两个`{...}`中间的内容并将其黏贴到VPS窗口中即可

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626145050.png)

选择你的Onedrive类型，我个人为开发者版，选`1`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626145149.png)

接下来的选项全部选`y`即可

最后，配置完成后退出配置页面

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626145253.png)

这个`name`以后会用到。

测试Onedrive连通性

```sh
rclone lsd onedrive:
```

如果有结果，则表示Onedrive已连接成功。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626145409.png)

---

## 安装Aria2以及AriaNG

方法一：直接使用[一键脚本](https://github.com/johnrosen1/vpstoolbox)，选择`扩展安装`，然后选中Aria2即可。

方法二：（手动安装，不推荐）：

> 网上已经烂大街了，而且在这不推荐此方法
> （so.在这只展示流程，具体操作自行Google）

1. 安装NGINX
2. 安装Acme.sh获取SSL证书
3. 安装Aria2以及AriaNG
4. 配置NGINX反向代理
5. 测试可用性

---

## 配置全自动上传脚本

[脚本](https://github.com/johnrosen1/vpstoolbox/blob/master/install/autoupload.sh)

如果你使用上面方法一的脚本安装的Aria2以及AriaNG，那么上传脚本的位置在/etc/aria2/

> 这个目录下的`aria2.conf`为Aria的主配置文件，`autoupload.sh`为上传脚本。

启用全自动上传脚本，默认为禁用状态。

```sh
cd /etc/aria2/
ls -l
nano aria2.conf
```

找到`# on-download-complete=/etc/aria2/autoupload.sh`，去掉前面的`# `即可，esc然后:wq回车退出。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626150503.png)

重启服务：`systemctl restart aria2`使配置生效，查看服务状态：`systemctl status aria2`

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626150721.png)

修改并配置全自动上传脚本

```sh
vim autoupload.sh
```

将下图的变量设置成之前配置的。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626150910.png)

更多可用的高级自定义配置，具体看下图。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626151024.png)

> 默认本机下载位置为`/usr/share/nginx/aria2/`，手动删除是可以的。

配置完成后esc然后:wq回车退出即可，立即生效。

---

## 测试可用性

登录AriaNG，一般为`https://$domain/ariang/`，修改以下内容，测试Aria连通性。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626151412.png)

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626151448.png)

已连线则表示连接成功。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626151538.png)

为了测试方便起见，选用[Debian iso](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/)进行测试。

https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-10.8.0-amd64-netinst.iso

如果一段时间后此文件出现在Onedrive里面，则测试成功。

如果测试失败，请自行检查问题所在，`/var/log/rclone/upload.log`为全自动上传脚本Log位置。
