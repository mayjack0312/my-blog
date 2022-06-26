# Rclone进阶——常用命令参数详解

## 目录

- [Rclone进阶——常用命令参数详解](#rclone进阶常用命令参数详解)
  - [目录](#目录)
  - [前言](#前言)
  - [安装Rclone](#安装rclone)
    - [Linux/macOS/BSD](#linuxmacosbsd)
    - [Windows](#windows)
  - [Rclone设置](#rclone设置)
  - [命令语法](#命令语法)
    - [用法示例](#用法示例)
  - [常用功能选项](#常用功能选项)
  - [常用参数](#常用参数)
  - [日志](#日志)
    - [输出日志到文件](#输出日志到文件)
  - [文件过滤](#文件过滤)
    - [文件类型过滤](#文件类型过滤)
    - [目录过滤](#目录过滤)
    - [文件大小过滤](#文件大小过滤)
    - [过滤规则文件](#过滤规则文件)
  - [环境变量](#环境变量)
    - [常用环境变量](#常用环境变量)

## 前言

Rclone是一个的命令行工具，支持在不同对象存储、网盘间同步、上传、下载数据。并且通过一些设置可以实现离线下载、服务器备份等非常实用的功能。本篇文章将讲解Rclone常用的一些命令参数。

## 安装Rclone

### Linux/macOS/BSD

官方提供了[一键安装脚本](https://rclone.org/install/#script-installati)：

```sh
curl https://rclone.org/install.sh | sudo bash
```

### Windows

[直接下载](https://rclone.org/downloads/)

## Rclone设置

`rclone config` —— 进入交互式配置选项，进行添加、删除、管理网盘等操作。详细操作参见：《[Rclone进阶——安装配置 连接OneDrive和Google Drive]()》

`rclone config file` —— 显示配置文件的路径，一般配置文件在 `~/.config/rclone/rclone.conf`

`rclone config show` —— 显示配置文件信息

## 命令语法

```txt
# 本地到网盘
rclone [功能选项] <本地路径> <网盘名称:路径> [参数] [参数] ...

# 网盘到本地
rclone [功能选项] <网盘名称:路径> <本地路径> [参数] [参数] ...

# 网盘到网盘
rclone [功能选项] <网盘名称:路径> <网盘名称:路径> [参数] [参数] ...
```

### 用法示例

```sh
rclone move -v /Download Onedrive:/Download --transfers=1
```

## 常用功能选项

- `rclone copy` —— 复制
- `rclone move` —— 移动，如果要在移动后删除空源目录，请加上`--delete-empty-src-dirs`参数
- `rclone sync` —— 同步：将源目录同步到目标目录，只更改目标目录。
- `rclone size` —— 查看网盘文件占用大小。
- `rclone delete` —— 删除路径下的文件内容。
- `rclone purge` —— 删除路径及其所有文件内容。
- `rclone mkdir` —— 创建目录。
- `rclone rmdir` —— 删除目录。
- `rclone rmdirs` —— 删除指定灵境下的空目录。如果加上`--leave-root`参数，则不会删除根目录。
- `rclone check` —— 检查源和目的地址数据是否匹配。
- `rclone ls` —— 列出指定路径下的所有的文件以及文件大小和路径。
- `rclone lsl` —— 比上面多一个显示上传时间。
- `rclone lsd` —— 列出指定路径下的目录
- `rclone lsf` —— 列出指定路径下的目录和文件

## 常用参数

- `-n` = `--dry-run` —— 测试运行，用来查看rclone在实际运行中会进行哪些操作。
- `-P` = `--progress` —— 显示实时传输进度，500mS刷新一次，否则默认1分钟刷新一次。
- `--cache-chunk-size SizeSuffi` —— 块的大小，默认5M，理论上是越大上传速度越快，同时占用内存也越多。如果设置得太大，可能会导致进程中断。
- `--cache-chunk-total-size SizeSuffix` —— 块可以在本地磁盘上占用的总大小，默认10G。
- `--transfers=N` —— 并行文件数，默认为4。在比较小的内存的VPS上建议调小这个参数，比如128M的小鸡上使用建议设置为1。
- `--config string` —— 指定配置文件路径，`string`为配置文件路径。
- `--ignore-errors` —— 跳过错误。比如 OneDrive 在传了某些特殊文件后会提示`Failed to copy: failed to open source object: malwareDetected: Malware detected`，这会导致后续的传输任务被终止掉，此时就可以加上这个参数跳过错误。但需要注意RCLONE的退出状态码不会为`0`。

## 日志

rclone有4个级别的日志记录，ERROR，NOTICE，INFO和DEBUG。默认情况下，rclone将生成ERROR和NOTICE级别消息。

- `-q` —— rclone将仅生成`ERROR`消息。
- `-v` —— rclone将生成`ERROR`，`NOTICE`和`INFO`消息，推荐此项。
- `-vv` —— rclone将生成`ERROR`，`NOTICE`，`INFO`和`DEBUG`消息。
- `--log-level LEVEL` —— 标志控制日志级别。

### 输出日志到文件

使用`--log-file=FILE`选项，`rclone`会将`Error`，`Info`和`Debug`消息以及标准错误重定向到`FILE`，这里的`FILE`是你指定的日志文件路径。

另一种方法是使用系统的指向命令，比如：

```sh
rclone sync -v Onedrive:/DRIVEX Gdrive:/DRIVEX > "~/DRIVEX.log" 2>&1
```

## 文件过滤

`--exclude` —— 排除文件或目录。
`--include` —— 包含文件或目录。
`--filter` —— 文件过滤规则，相当于上面两个选项的其它使用方式。包含规则以`+`开头，排除规则以`-`开头。

### 文件类型过滤

比如`--exclude "*.bak"`、`--filter "- *.bak"`，排除所有`bak`文件。也可以写作。
比如`--include "*.{png,jpg}"`、`--filter "+ *.{png,jpg}"`，包含所有`png`和`jpg`文件，排除其他文件。
`--delete-excluded`删除排除的文件。需配合过滤参数使用，否则无效。

### 目录过滤

目录过滤需要在目录名称后面加上`/`，否则会被当做文件进行匹配。以`/`开头只会匹配根目录（指定目录下），否则匹配所目录。这同样适用于文件。
`--exclude ".git/"`排除所有目录下的`.git`目录。
`--exclude "/.git/"`只排除根目录下的`.git`目录。
`--exclude "{Video,Software}/"`排除所有目录下的`Video`和`Software`目录。
`--exclude "/{Video,Software}/"`只排除根目录下的`Video`和`Software`目录。
`--include "/{Video,Software}/**"`仅包含根目录下的`Video`和`Software`目录的所有内容。

### 文件大小过滤

默认大小单位为`kBytes`，但可以使用`k`，`M`或`G`后缀。
`--min-size`过滤小于指定大小的文件。比如`--min-size 50`表示不会传输小于50k的文件。
`--max-size`过滤大于指定大小的文件。比如`--max-size 1G`表示不会传输大于1G的文件。

> **TIPS：** 博主在实际使用中发现大小过滤两个选项不能同时使用。

### 过滤规则文件

`--filter-from <规则文件>`从文件添加包含 / 排除规则。比如`--filter-from filter-file.txt`。

过滤规则文件示例：

```txt
- secret*.jpg
+ *.jpg
+ *.png
+ file2.avi
- /dir/Trash/**
+ /dir/**
- *
```

这里只举例比较常用和简单的一些过滤用法，更复杂和高端的用法可以查看[官方文档](https://rclone.org/filtering/)。

## 环境变量

rclone中的每个选项都可以通过环境变量设置。环境变量的名称可以通过长选项名称进行转换，删除`--`前缀，更改`-`为`_`，大写并添加前缀`RCLONE_`。环境变量的优先级会低于命令行选项，即通过命令行追加相应的选项时会覆盖环境变量设定的值。

比如设置最小上传大小`--min-size 50`，使用环境变量是`RCLONE_MIN_SIZE=50`。当环境变量设置后，在命令行中使用`--min-size 100`，那么此时环境变量的值就会被覆盖。

### 常用环境变量

- `RCLONE_CONFIG` —— 自定义配置文件路径
- `RCLONE_CONFIG_PASS` —— 若 rclone 进行了加密设置，把此环境变量设置为密码，可自动解密配置文件。
- `RCLONE_RETRIES` —— 上传失败重试次数，默认3次
- `RCLONE_RETRIES_SLEEP` —— 上传失败重试等待时间，默认禁用，单位`s`、`m`、`h`分别代表秒、分钟、小时。
- `CLONE_TRANSFERS` —— 并行上传文件数。
- `RCLONE_CACHE_CHUNK_SIZE` —— 块的大小，默认5M，理论上是越大上传速度越快，同时占用内存也越多。如果设置得太大，可能会导致进程中断。
- `RCLONE_CACHE_CHUNK_TOTAL_SIZE` —— 块可以在本地磁盘上占用的总大小，默认10G。
- `RCLONE_IGNORE_ERRORS=true` —— 跳过错误。

