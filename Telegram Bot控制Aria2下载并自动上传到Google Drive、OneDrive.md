# Telegram Bot控制Aria2下载并自动上传到Google Drive、OneDrive

## 目录

- [Telegram Bot控制Aria2下载并自动上传到Google Drive、OneDrive](#telegram-bot控制aria2下载并自动上传到google-driveonedrive)
  - [目录](#目录)
  - [前言](#前言)
  - [部署Aria2](#部署aria2)
  - [获取Telegram Bot访问令牌](#获取telegram-bot访问令牌)
  - [获取用户ID](#获取用户id)
  - [部署tele-aria2](#部署tele-aria2)
  - [使用Bot控制Aria2下载](#使用bot控制aria2下载)
  - [局限性](#局限性)
  - [结尾](#结尾)

## 前言

[tele-aria2](https://github.com/mayjack0312/tele-aria2)是一个可以控制Aria2服务端的Telegram Bot，它可以发任务到远程Aria2进行下载、查看任务、删除任务等操作。准确来说它是一个Aria2前端程序，只不过形式不是我们所熟知的Ar­i­aNg那样的Web页面，而是以一个聊天机器人的形式存在。如果Aria2配置好联动Rclone自动上传，那么就可以实现发送下载链接给Bot→Aria2下载→Rclone自动上传文件到Google Drive等网盘的骚操作。

## 部署Aria2

[Aria2一键安装脚本](https://github.com/mayjack0312/my-blog/blob/main/%E9%AB%98%E7%BA%A7%E8%BF%9B%E9%98%B6%E7%BB%84%E5%90%88%E7%8E%A9%E6%B3%95%E2%80%94%E2%80%94Aria2%2BRclone%E5%AE%9E%E7%8E%B0OneDrive%E3%80%81Google%20Drive%E7%AD%89%E7%BD%91%E7%9B%98%E7%A6%BB%E7%BA%BF%E4%B8%8B%E8%BD%BD.md)(GNU/​Linux)和[Aria2 Pro](https://github.com/mayjack0312/Aria2-Pro-Docker)(Docker)任选其一，这里不做赘述了。如果需要自动上传那么就需要配置联动Rclone的步骤，能正常下载并上传即代表部署工作完成，否则忽略这个步骤即可。

## 获取Telegram Bot访问令牌

使用[@BotFather](https://telegram.me/botfath)创建自己的Bot并获取访问令牌（`<TOKEN>`），过程如图所示：

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626125753.png)

## 获取用户ID

- **给刚创建Bot随便发送一个信息**
- 访问`https://api.telegram.org/bot<TOKEN>/getUpdates`（替换`<TOKEN>`字段），会获得一长串信息，其中`"id":`字段后面的数字就是所要获取的用户ID。

## 部署tele-aria2

为了防止环境不统一导致部署失败将使用 Docker 进行部署。

  - 首先编写一个[配置文件](https://github.com/mayjack0312/tele-aria2#configuration-file-examp)，从上到下依次是Aria2 RPC地址、密钥、代理地址、Bot访问令牌、用户 ID、任务显示数量最大值。根据自己的实际情况填写，自由地区的VPS可以去掉代理地址配置。

```json
{
  "aria2-server": "ws://0.0.0.0:0000/jsonrpc",
  "aria2-key": "xxxx",
  "proxy": "http://127.0.0.1:0000",
  "bot-key": "123456789:0000",
  "user-id": "123456",
  "max-index": 10
}
```

> **TIPS：** 如果你需要多用户支持，用户ID之间用,(半角逗号)隔开即可，就像这样：`"user-id": "123,456,789"`

- 把编写好的配置文件保存到`~/.tele-aria2-conf.json`（这个文件名称和路径随意，自己知道就行，后面的 Docker 运行命令中记得替换）

```sh
vim ~/.tele-aria2-conf.json
```

- 运行Docker容器

```docker
docker run -d \
    --name tele-aria2 \
    --restart unless-stopped \
    --log-opt max-size=1m \
    --network host \
    -v ~/.tele-aria2-conf.json:/config.json \
    p3terx/tele-aria2:0.2.2
```

- 发送`/start`给Bot，Bot回复并弹出菜单即代表部署成果。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626130737.png)

## 使用Bot控制Aria2下载

- 发送链接或者种子文件即可开始下载，下载完成会有通知。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/26/20220626130834.png)

- 如果配置好了自动上传，那么等待上传完成即可。

## 局限性

- 无法在Bot中直接查看上传进度，毕竟只是Aria2的前端。
- 暂时一个Bot只能控制一个Aria2服务端。

## 结尾

tele-aria2目前尚处于初级开发阶段，功能比较极简，不过Aria2绝大多数时间的使用场景是添加、暂停、删除任务。Ar­i­aNg功能强大，但由于开发者为了保持操作逻辑的严谨性（添加任务前可修改默认参数），因此牺牲掉批量添加种子功能，而tele-aria2可以一次性发送多个种子来实现批量添加种子，一定程度上也弥补了Ar­i­aNg这个功能的缺失。所以说在日常使用场景中tele-aria2可以替代Ar­i­aNg，而在需要单独定制任务参数或者查看任务进度时使用Ar­i­aNg，二者相辅相成应该算是目前Aria2前端最佳解决方案。

