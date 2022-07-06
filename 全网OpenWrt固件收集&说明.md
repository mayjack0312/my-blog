# OpenWrt固件相关

## 目录

- [OpenWrt固件相关](#openwrt固件相关)
  - [目录](#目录)
    - [原生OpenWrt固件](#原生openwrt固件)
    - [Snapshot版原生固件](#snapshot版原生固件)
      - [KoolShare iStoreOS](#koolshare-istoreos)
      - [骷髅头 DHDAXCW](#骷髅头-dhdaxcw)
      - [Kiddin9](#kiddin9)
      - [QiuSimons（404）](#qiusimons404)
      - [Quintus Chu（502）](#quintus-chu502)
        - [R2C+R2S+R4S](#r2cr2sr4s)
        - [R4S独立版](#r4s独立版)
      - [SuLingGG](#sulinggg)
      - [天灵（ctcgfw）](#天灵ctcgfw)
      - [墨子冥](#墨子冥)
      - [Chuck](#chuck)
    - [Friendlywrt固件](#friendlywrt固件)
      - [Dayong](#dayong)
      - [FriendlElec](#friendlelec)
      - [Song Chenwen](#song-chenwen)
      - [MeltNet](#meltnet)
    - [注意事项](#注意事项)
    - [友链](#友链)
      - [个人推荐固件 *排名不分先后*](#个人推荐固件-排名不分先后)
      - [个人不推荐固件](#个人不推荐固件)
      - [不在这篇文章提到的的开发者](#不在这篇文章提到的的开发者)

---

### 原生OpenWrt固件

 [原生固件源码](https://github.com/openwrt/openwrt)

---

### Snapshot版原生固件

#### KoolShare iStoreOS

默认登录ip：http://192.168.100.1  
账号 ：root  
默认密码：password  
1.r5s-sdcard-xxxx.img.gz为写盘文件，不需要解压，可直接写入TF卡启动。  
2.r5s-update-xxxx.img为另外的升级方法使用的固件，具体请看[链接](https://doc.linkease.com/zh/guide/istoreos/install_r5s.html)

#### 骷髅头 DHDAXCW

R2C默认登录ip：192.168.2.1  
R2S&R4S默认ip：192.168.11.1  
账号 ：root  
密码：password

#### Kiddin9

自行编译：https://supes.top  
R2C默认登录ip：op/或10.0.0.1(若后台无法打开，请尝试插拔交换wan、lan网线顺序)

#### QiuSimons（404）

这个大佬的源码好像都是基于Docker的  
如有需要使用的请自行去大佬的GitHub Releases

#### Quintus Chu（502）

##### R2C+R2S+R4S

默认登录ip：192.168.1.1  
账号：root  
密码：默认无密码（登录后尽快设置密码）

##### R4S独立版

默认登录ip：192.168.1.1  
账号：root  
密码：默认无密码（登录后尽快设置密码）

#### SuLingGG

具体自行测试，这项目相关内容我啥也没找到 OwO

#### 天灵（ctcgfw）

貌似在大佬的频道里有固件文件 **（需要用命令刷）**

#### 墨子冥

更偏向于个人编译系统 **（可能不适合通用人群）**

#### Chuck

默认登录ip：192.168.33.1  
账号：root  
密码：fa

---

### Friendlywrt固件

#### Dayong

默认登录ip：192.168.2.1  
账号：root  
密码：password  

#### FriendlElec

这套'官方'固件我还没玩明白，发了一大堆文件 不知道下哪个 (=′ー`)

#### Song Chenwen

默认登录ip：192.168.2.1  
账号：root  
密码：password

#### MeltNet

什么描述信息也没有，自己Google查查吧

---

### 注意事项

都是ARM架构的，X86_64需自行构建  
后台ip、账户、密码——>你用谁的固件就找谁的  
尽量不要用现成的恢复备份（当然，部分config也给你了）  
WAN和LAN自己区分(不同固件也会导致这两个口的使用不同)  
以上版本都是基于**正式版**（相关源码、with的Docker以及其他版本需自行去对应的 **GitHub Releases**下载）

### 友链

- **个人不推荐刷原生系统** *具体原因自行google*

- [OP原生](https://github.com/openwrt/openwrt)

#### 个人推荐固件 *排名不分先后*

- [骷髅头DHDAXCW全系列](https://github.com/DHDAXCW/)
- [Dayong-R1S+R2S+R4S](https://github.com/klever1988/nanopi-openwrt)
- [KoolShare iStoreOS全系列](https://fw.koolcenter.com/iStoreOS/)
- [Kiddin9全系列](https://github.com/kiddin9/OpenWrt_x86-r2s-r4s-r5s-N1)
- [LEAN](https://github.com/coolsnowwolf/lede)

#### 个人不推荐固件

- [404大佬全系列](https://github.com/QiuSimons/YAOF)
- [502大佬R2S+R4S整合版](https://github.com/quintus-lab/OpenWRT-Rockchip)
- [502大佬R4S独立版](https://github.com/quintus-lab/NanoPi-R4S-OpenWRT)
- [ctcgfw大佬TG更新频道-R2S](https://t.me/nanopi_r2s)
- [墨子冥-R2S+R4S](https://github.com/msylgj/R2S-R4S-OpenWrt)
- [Chuck-R2S](https://github.com/fanck0605/openwrt-nanopi-r2s)
- [FriendlElec-R2S](https://download.friendlyelec.com/nanopir2s)
- [FriendlElec-R4S](https://download.friendlyelec.com/nanopir4s)
- [Song Chenwen-R2S](https://github.com/songchenwen/nanopi-r2s)
- [SuLingGG全系列](https://openwrt.cc/releases/targets/rockchip/armv8/)
- [MeltNet-R2S](https://share.weiyun.com/J6cHAY9I#Copyri)

★★★ **部分全系列不包括R5S**

#### 不在这篇文章提到的的开发者

- *这类使用体验不清楚，需要自行测试*

- [soffchen](https://github.com/soffchen/)
- [thomaswcy](https://github.com/thomaswcy/)
