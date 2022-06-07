---
title: 两台linux 互相传输文件
author: chaizz
date: 2021-02-02 15:42:51
tags: Linux
---



> #### 百度百科
>
> ###### rsync是linux系统下的数据[镜像](https://baike.baidu.com/item/镜像)[备份](https://baike.baidu.com/item/备份)工具。使用快速增量备份工具Remote Sync可以远程同步，支持本地复制，或者与其他SSH、rsync主机同步。
>
> ##### 它的特性如下：
>
> - 可以[镜像](https://baike.baidu.com/item/镜像)保存整个目录树和文件系统。
> - 可以很容易做到保持原来文件的权限、时间、软硬链接等等。
> - 无须特殊权限即可安装。
>
> <!--more-->
>
> - 快速：第一次同步时 rsync 会复制全部内容，但在下一次只传输修改过的文件。rsync 在传输数据的过程中可以实行压缩及[解压缩](https://baike.baidu.com/item/解压缩)操作，因此可以使用更少的带宽。
> - 安全：可以使用scp、ssh等方式来传输文件，当然也可以通过直接的socket连接。
> - 支持匿名传输，以方便进行网站镜像。 



#### 1、Rsync参数说明

1. 使用 rsync + ssh 进行加密传输  

   ```shell
   rsync -avzhe ssh /home/ubuntu/remote_test ubuntu@193.112.102.63:/home/ubuntu/remote_test
   ```

2. 参数设置  
```
-v, --verbose 详细模式输出
   -q, --quiet 精简输出模式
   -c, --checksum 打开校验开关，强制对文件传输进行校验
   -a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD
   -r, --recursive 对子目录以递归模式处理
   -R, --relative 使用相对路径信息
   -b, --backup 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。
   --backup-dir 将备份文件(如~filename)存放在在目录下。
   -suffix=SUFFIX 定义备份文件前缀
   -u, --update 仅仅进行更新，也就是跳过所有已经存在于DST，并且文件时间晚于要备份的文件。(不覆盖更新的文件)
   -l, --links 保留软链结
   -L, --copy-links 像对待常规文件一样处理软链接
   --copy-unsafe-links 仅仅拷贝指向SRC路径目录树以外的链接
   --safe-links 忽略指向SRC路径目录树以外的链接
   -H, --hard-links 保留硬链接
   -p, --perms 保持文件权限
   -o, --owner 保持文件属主信息
   -g, --group 保持文件属组信息
   -D, --devices 保持设备文件信息
   -t, --times 保持文件时间信息
   -S, --sparse 对稀疏文件进行特殊处理以节省DST的空间
   -n, --dry-run显示哪些文件将被传输
   -W, --whole-file 拷贝文件，不进行增量检测
   -x, --one-file-system 不要跨越文件系统边界
   -B, --block-size=SIZE 检验算法使用的块尺寸，默认是700字节
   -e, --rsh=COMMAND 指定使用rsh、ssh方式进行数据同步
   --rsync-path=PATH 指定远程服务器上的rsync命令所在路径信息
   -C, --cvs-exclude 使用和CVS一样的方法自动忽略文件，用来排除那些不希望传输的文件
   --existing 仅仅更新那些已经存在于DST的文件，而不备份那些新创建的文件
   --delete 删除那些DST中SRC没有的文件
   --delete-excluded 同样删除接收端那些被该选项指定排除的文件
   --delete-after 传输结束以后再删除
   --ignore-errors 即使出现IO错误也进行删除
   --max-delete=NUM 最多删除NUM个文件
   --partial 保留那些因故没有完全传输的文件，以是加快随后的再次传输
   --force 强制删除目录，即使不为空
   --numeric-ids 不将数字的用户和组ID匹配为用户名和组名
   --timeout=TIME IP超时时间，单位为秒
   -I, --ignore-times 不跳过那些有同样的时间和长度的文件
   --size-only 当决定是否要备份文件时，仅仅察看文件大小而不考虑文件时间
   --modify-window=NUM 决定文件是否时间相同时使用的时间戳窗口，默认为0
   -T --temp-dir=DIR 在DIR中创建临时文件
   --compare-dest=DIR 同样比较DIR中的文件来决定是否需要备份
   -P 等同于 --partial
   --progress 显示备份过程
   -z, --compress 对备份的文件在传输时进行压缩处理
   --exclude=PATTERN 指定排除不需要传输的文件模式
   --include=PATTERN 指定不排除而需要传输的文件模式
   --exclude-from=FILE 排除FILE中指定模式的文件
   --include-from=FILE 不排除FILE指定模式匹配的文件
   --version 打印版本信息
   --address 绑定到特定的地址
   --config=FILE 指定其他的配置文件，不使用默认的rsyncd.conf文件
   --port=PORT 指定其他的rsync服务端口
   --blocking-io 对远程shell使用阻塞IO
   -stats 给出某些文件的传输状态
   --progress 在传输时显示传输过程
   --log-format=formAT 指定日志文件格式
   --password-file=FILE 从FILE中得到密码
   --bwlimit=KBPS 限制I/O带宽，KBytes per second
   -h, --help 显示帮助信息
```


#### 2、目的

- 实现两台服务器之间做增量备份。A服务器作为服务端，B服务器作为客户端。

- B服务器每天定时将某个目录下产生的文件发送到A服务器中。

#### 3、操作方法

在A服务器中安装 rsync ，查看服务器是否安装rsync。


```
rsync --version
#显示如下  代表已经安装  版本为：3.1.1  (确保两台服务器之前安装的版本的一致，不一致可能造成无法同步数据)
rsync  version 3.1.2  protocol version 31
Copyright (C) 1996-2015 by Andrew Tridgell, Wayne Davison, and others.
Web site: http://rsync.samba.org/
Capabilities:
    64-bit files, 64-bit inums, 64-bit timestamps, 64-bit long ints,
    socketpairs, hardlinks, symlinks, IPv6, batchfiles, inplace,
    append, ACLs, xattrs, iconv, symtimes, prealloc

rsync comes with ABSOLUTELY NO WARRANTY.  This is free software, and you
are welcome to redistribute it under certain conditions.  See the GNU
General Public Licence for details.
```

两边都安装完成了以后，就可以使用了。

```shell
rsync -avzhe ssh 001 ubuntu@193.112.102.63:/home/ubuntu/remote_test   
# 使用ssh模式连接 需要输入密码
```

##### 1. 将A服务器作为服务端

编辑文件 `/etc/vi /etc/rsyncd.conf`默认不存在， 直接创建 。编辑输入如下内容：

```shell
#服务端配置文件 (和上面一样)
motd file = /etc/rsyncd.motd
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsyncd.lock
log file = /var/log/rsyncd.log

[workspace]  #模块名 在客户端需要用到
path = /devdata/backres   #需要备份的路径
comment = Test
uid = root   #使用的用户
gid = root   #同上 
read only = false   #是否可写
auth users = root   #验证的用户
secrets file = /etc/rsyncd.pass   #密码文件  文件的权限：600
transfer logging = yes   #日志输出
hosts allow=123.207.165.19   #允许的可短的IP地址

```

新建密码文件 在上述的conf文件中，倒数第三行。内容为当前用户名以及密码 。格式：user：password。密码文件拥有用户必须为当前用户 ubuntu 且权限必须为 600

```shell
ubuntu:123456
```

查看rsync 状态 ： `service rsync status`。 重启rsync服务： `service rsync restart`

##### 2. 将b服务器作为客户端

客户端不需要设置太多内容可直接运行命令，不过需要手动输入密码，可将密码存在一个文件内，使用`--password-file=FILE` 进行从文件中读取密码。这个文件路径任意。文件内容只包含服务端密码即可。但是必须为root用户拥有且权限为600。

使用命令 ：

```shell
sudo rsync -aqr --password-file=/home/ubuntu/remote_test/rsyncd.pass /home/ubuntu/remote_test ubuntu@193.112.22.194::workspace
```

```shell
#参数解释：
-a：--archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，
-q：--quiet 精简输出模式
-r：--recursive 对子目录以递归模式处理
workspace：即服务端的模块名
```

##### 3. 设置定时任务

使用ubuntu 的crontab 

```shell
crontab -e
```

第一次打开会选择的编辑器，一般选择vim,输入数字即可。如下所示：

```shell
Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/vim.basic
  4. /usr/bin/vim.tiny
```

定时任务格式：

```shell
* * * * * command    
用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下：
minute hour day month week command
其中：
minute：表示分钟，可以是从0到59之间的任何整数。
hour：表示小时，可以是从0到23之间的任何整数。
day：表示日期，可以是从1到31之间的任何整数。
month：表示月份，可以是从1到12之间的任何整数。
week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件
/ 斜杠代表频率 例如：
如果想每分钟都执行一次的话就采用默认的 * * * * *
如果想每五分钟执行一次可以 */5 * * * * 
如果是每两个小时执行一次的话 那就是 *  */2 * * *来设置;
```

在定时任务中输入：

```shell
30 23 * * * sudo rsync -r --password-file=/home/ubuntu/remote_test/rsyncd.pass /home/ubuntu/remote_test ubuntu@193.112.22.194::workspace
#表示 在每天的23:30 进行数据备份
```



