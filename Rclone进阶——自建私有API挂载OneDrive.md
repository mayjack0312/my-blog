# Rclone进阶——自建私有API挂载OneDrive

## 目录

- [Rclone进阶——自建私有API挂载OneDrive](#rclone进阶自建私有api挂载onedrive)
  - [目录](#目录)
  - [前言](#前言)
  - [速度测试](#速度测试)
  - [创建OneDrive API](#创建onedrive-api)
    - [获取Client ID](#获取client-id)
    - [获取Client secret](#获取client-secret)
  - [设置API权限](#设置api权限)
  - [获取token](#获取token)
  - [Rclone连接OneDrive](#rclone连接onedrive)
  - [挂载OneDrive](#挂载onedrive)
  - [其它说明](#其它说明)
    - [私有API局限性](#私有api局限性)
    - [OneDrive API限制阈值是多少？](#onedrive-api限制阈值是多少)
    - [如何避免OneDrive API受限？](#如何避免onedrive-api受限)
    - [关于E5自动续订](#关于e5自动续订)

## 前言

使用Rclone给OneDrive传输文件时可能会遇到速度非常慢、断联等一些问题，其根源是触发了OneDrive API的限制，而默认的Rclone内置API由于非常多人在同时在使用所以这些问题也就愈发明显。使用自建的私有API连接OneDrive可大幅改善这些情况，而且对于三个月的Of­fice 365 E5开发者试用版，平时偶尔用用Rclone还会有自动续订的可能性，比起刻意刷API的方式更为安全和稳定。此外自建的API还可以给其它的账号和其它应用使用。

## 速度测试

前者为使用Rclone自带API，后者为自建API，可以很明显的看出速度相差了10多倍。

![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625114824.png)

## 创建OneDrive API

### 获取Client ID

-  进入[Microsoft Azure应用注册](https://portal.azure.com/#home)页面。点击`新注册`。**(首选Chrome浏览器)**

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625115435.png)

- 名称随意，账户类型选择最后一个，重定向URL填写`http://localhost`。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625115930.png)

- 创建成功后你会看到Client ID（客户端ID），复制并保存好。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625120320.png)

### 获取Client secret

- 点击`证书和密码`，按照图中序号的顺序操作添加密码。

    ![image ](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625120840.png)

- 然后你会看到Client secret（客户端密码），复制并保存好。

    ![image ](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625121031.png)

## 设置API权限

- 点击`API权限`，按照图示进行操作，添加`Files.Read`、`Files.ReadWrite`、`Files.Read.All`、`Files.ReadWrite.All`、`offline_access`、`User.Read`这些权限。

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625121508.png)

- 最后确认下权限是否添加完整。**(按照上面要添加的内容来，我这个不全)**

    ![image](https://cdn.jsdelivr.net/gh/mayjack0312/image@main/2022/06/25/20220625121637.png)

## 获取token

- 在本地电脑上[下载rclone](https://rclone.org/downloads/)。
- 以Windows为例子，解压并进入`rclone.exe`所在文件夹，在资源管理器地址栏输入`cmd`，回车就会在当前路径打开命令提示符。
- 替换以下命令中的`Client_ID`、`Client_secret`并执行。

```sh
rclone authorize "onedrive" "Client_ID" "Client_secret"
```

接下来会弹出浏览器，要求你登录账号进行授权。授权完后命令提示符窗口会出现以下信息：

```sh
If your browser doesn't open automatically go to the following link: http://127.0.0.1:53682/auth
Log in and authorize rclone for access
Waiting for code...
Got code
Paste the following into your remote machine --->
{"access_token":"xxxxxxxxxxxxxxxxxx","expiry":"2022-06-25T12:30:00.5036298+08:00"}
<---End paste
```

{"access_token":"xxxxxxxxxxxxxxxxxx","expiry":"2022-06-25T12:30:00.5036298+08:00"} 整个内容（包含括号）就是 to­ken ，复制并保存好。

## Rclone连接OneDrive

输入`rclone config`命令，会出现以下信息，参照下面的注释进行操作。

</details>
<details><summary>点击查看</summary>
TIPS： 因为RCLONE会时不时进行更新，当你看到这篇教程时菜单选项可能已经发生了略微的改动，但大致思路不会变，不要无脑照搬操作。

```conf
$ rclone config

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> n # 输入 n，新建
name> od-e5-api # 输入网盘名称，类似于标签，用于区分不同的网盘。
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / 1Fichier
   \ "fichier"
 2 / Alias for an existing remote
   \ "alias"
 3 / Amazon Drive
   \ "amazon cloud drive"
 4 / Amazon S3 Compliant Storage Provider (AWS, Alibaba, Ceph, Digital Ocean, Dreamhost, IBM COS, Minio, etc)
   \ "s3"
 5 / Backblaze B2
   \ "b2"
 6 / Box
   \ "box"
 7 / Cache a remote
   \ "cache"
 8 / Citrix Sharefile
   \ "sharefile"
 9 / Dropbox
   \ "dropbox"
10 / Encrypt/Decrypt a remote
   \ "crypt"
11 / FTP Connection
   \ "ftp"
12 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
13 / Google Drive
   \ "drive"
14 / Google Photos
   \ "google photos"
15 / Hubic
   \ "hubic"
16 / JottaCloud
   \ "jottacloud"
17 / Koofr
   \ "koofr"
18 / Local Disk
   \ "local"
19 / Mail.ru Cloud
   \ "mailru"
20 / Mega
   \ "mega"
21 / Microsoft Azure Blob Storage
   \ "azureblob"
22 / Microsoft OneDrive
   \ "onedrive"
23 / OpenDrive
   \ "opendrive"
24 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
25 / Pcloud
   \ "pcloud"
26 / Put.io
   \ "putio"
27 / QingCloud Object Storage
   \ "qingstor"
28 / SSH/SFTP Connection
   \ "sftp"
29 / Transparently chunk/split large files
   \ "chunker"
30 / Union merges the contents of several remotes
   \ "union"
31 / Webdav
   \ "webdav"
32 / Yandex Disk
   \ "yandex"
33 / http Connection
   \ "http"
34 / premiumize.me
   \ "premiumizeme"
Storage> 22 # 选择 Microsoft OneDrive ，截止2020年2月23日序号为 22
** See help for onedrive backend at: https://rclone.org/onedrive/ **

Microsoft App Client Id
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_id> # 输入 Client Id （客户端 ID）
Microsoft App Client Secret
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_secret> # 输入 Client Secret （客户端密码）
Edit advanced config? (y/n)
y) Yes
n) No
y/n> n # 输入 n
Remote config
Make sure your Redirect URL is set to "http://localhost:53682/" in your custom config.
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes
n) No
y/n> n # 输入 n
For this to work, you will need rclone available on a machine that has a web browser available.
Execute the following on your machine (same rclone version recommended) :
    rclone authorize "onedrive" "client_id" "client_secret"
Then paste the result below:
result> {"access_token":"XXXXXXXXX","expiry":"2020-02-22T21:18:39.5036298+08:00"}   # 输入 token
Choose a number from below, or type in an existing value
 1 / OneDrive Personal or Business
   \ "onedrive"
 2 / Root Sharepoint site
   \ "sharepoint"
 3 / Type in driveID
   \ "driveid"
 4 / Type in SiteID
   \ "siteid"
 5 / Search a Sharepoint site
   \ "search"
Your choice> 1 # # 这里问你要选择的类型，因为是使用 OneDrive ，所以输入 1
Found 1 drives, please select the one you want to use:
0: OneDrive (business) id=xxxxxxxxxxxxxx
Chose drive to use:> 0 # 检测到了网盘，这里编号是 0 ，所以输入 0
Found drive 'root' of type 'business', URL: https://p3terx-my.sharepoint.com/personal/xxxxxx/Documents
Is that okay?
y) Yes
n) No
y/n> y # 问你是否确认，如果没问题则输入 y

--------------------
[od-e5-api]
type = onedrive
client_id = xxxxxxxxxx
client_secret = xxxxxxxxxxxxxxxx
token = {"access_token":"xxxxxxxxxxxxxxxxxx","expiry":"2020-02-22T21:18:39.5036298+08:00"}
drive_id = xxxxxxxxxxxxxxxxxxxxxxxxx
drive_type = business
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y # 最后会显示网盘的配置信息并询问你是否正确，如果没问题则输入 y
Current remotes:

Name                 Type
====                 ====
od-e5-api            onedrive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q # 输入 q 退出
```

最后验证一下是否能正常使用，替换以下命令中的`od-e5-api`并执行，注意最后有:(冒号)。网盘中出现`test`文件即表示成功。

```sh
touch test && rclone move test od-e5-api:
```

至此，Rclone已成功使用自建API连接到了OneDrive网盘。
</details>

## 挂载OneDrive

如果你需要挂载到本地目录，可以参考《[Rclone使用 —— 挂载OneDrive、Google Drive等网盘(Linux)]()》

## 其它说明

### 私有API局限性

**！！！自建私有API虽然能改善上传体验，但如果使用过于频繁还是会上传受限。**

### OneDrive API限制阈值是多少？

微软没有明确说明OneDrive API的限制，以下为官方文档原文：

>根据使用情况，我们会对阈值进行微调，以便用户可以使用最大数量的资源，而不会降低可靠性和性能。

通过查看其它类型的API限制说明可以猜测到的是有总次数和频率这个两种限制。总次数是指一天内所能调用的次数，而频率为每分钟所能调用的次数。而一旦达到限制阈值，那么就会限制文件的上传。

### 如何避免OneDrive API受限？

**！！！不要在短时间内上传过多的文件，文件大小无所谓，关键在于文件数量。**

### 关于E5自动续订

以目前我的开发者试用版使用经验来看，只要你使用自建的私有API就都有可能续订，至于频率并没有一个标准，也并不是越多越好。刻意刷API可能得不偿失，尤其是使用GitHub Ac­tions **（不但续订不成功，而且还会触发GA封号机制）**，因为服务器是微软Azure的，那么多人以相似的方式无意义的刷API想要识别是非常容易的。
